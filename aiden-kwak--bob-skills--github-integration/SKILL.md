---
name: github-integration
description: Use this skill when the user wants to integrate GitHub OAuth authentication, create repositories programmatically, commit files to GitHub, or set up automatic repository creation for projects. Triggers include: mentions of 'GitHub integration', 'GitHub OAuth', 'create GitHub repo', 'commit to GitHub', 'GitHub API', 'repository automation', or requests to connect a Django/Next.js application with GitHub. Also use when implementing social authentication with GitHub, managing GitHub tokens securely, or building features that automatically push code to GitHub repositories. This skill covers both backend (Django with django-allauth and PyGithub) and frontend (Next.js/React) implementation.
metadata:
  author: aiden-kwak
---

# GitHub Integration for Django + Next.js Applications

## Overview

Complete GitHub integration including OAuth authentication, repository management, and automatic code commits. Built with Django (backend) and Next.js (frontend).

## Tech Stack

- **Backend**: Django, django-allauth, PyGithub, cryptography
- **Frontend**: Next.js, TypeScript, React
- **Authentication**: GitHub OAuth 2.0
- **API**: GitHub REST API v3

## Quick Reference

| Task | Approach |
|------|----------|
| OAuth Setup | django-allauth + GitHub OAuth App |
| Repository Creation | PyGithub client wrapper |
| File Commits | GitHub API with base64 encoding |
| Token Security | Fernet encryption |
| Frontend Auth | OAuth callback handling |

---

## Backend Implementation

### 1. Dependencies

```bash
# requirements.txt
Django>=4.2.0
djangorestframework>=3.14.0
django-allauth>=0.57.0
PyGithub>=2.1.1
cryptography>=41.0.0
django-cors-headers>=4.3.0
python-dotenv>=1.0.0
```

### 2. Django Settings Configuration

```python
# config/settings.py
from pathlib import Path
import os
from dotenv import load_dotenv

BASE_DIR = Path(__file__).resolve().parent.parent
load_dotenv(BASE_DIR / '.env')

INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    # Third party apps
    "rest_framework",
    "corsheaders",
    "django.contrib.sites",  # Required for allauth
    "allauth",
    "allauth.account",
    "allauth.socialaccount",
    "allauth.socialaccount.providers.github",  # GitHub provider
    # Local apps
    "projects",
    "agents",
    "tasks",
    "github_integration",
]

SITE_ID = 1

MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "corsheaders.middleware.CorsMiddleware",  # Must be before CommonMiddleware
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "django.middleware.csrf.CsrfViewMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
    "django.contrib.messages.middleware.MessageMiddleware",
    "django.middleware.clickjacking.XFrameOptionsMiddleware",
    "allauth.account.middleware.AccountMiddleware",  # Required for allauth
]

# CORS Settings
CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",
    "http://127.0.0.1:3000",
]
CORS_ALLOW_CREDENTIALS = True

# CSRF Settings
CSRF_TRUSTED_ORIGINS = [
    "http://localhost:3000",
    "http://127.0.0.1:3000",
]

# Session Settings
SESSION_COOKIE_SAMESITE = None  # Allow cross-origin
SESSION_COOKIE_SECURE = False  # Set to True in production with HTTPS
SESSION_COOKIE_HTTPONLY = True
CSRF_COOKIE_SAMESITE = None
CSRF_COOKIE_SECURE = False  # Set to True in production with HTTPS
CSRF_COOKIE_HTTPONLY = False  # Allow JavaScript to read CSRF cookie

# REST Framework Settings
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.AllowAny',
    ],
    'DEFAULT_RENDERER_CLASSES': [
        'rest_framework.renderers.JSONRenderer',
    ],
}

# Django Allauth Settings
AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
    'allauth.account.auth_backends.AuthenticationBackend',
]

# Redirect to frontend after login
LOGIN_REDIRECT_URL = 'http://localhost:3000/auth/callback'
ACCOUNT_LOGOUT_REDIRECT_URL = 'http://localhost:3000'

# Allauth settings
ACCOUNT_EMAIL_VERIFICATION = 'none'
ACCOUNT_AUTHENTICATION_METHOD = 'username'
ACCOUNT_EMAIL_REQUIRED = False
SOCIALACCOUNT_AUTO_SIGNUP = True
SOCIALACCOUNT_STORE_TOKENS = True  # CRITICAL: Store OAuth tokens

# GitHub provider specific settings
SOCIALACCOUNT_PROVIDERS = {
    'github': {
        'SCOPE': [
            'user',
            'repo',
            'read:org',
        ],
    }
}
```

### 3. GitHub Client Implementation

```python
# github_integration/client.py
from github import Github as GithubAPI, GithubException
from cryptography.fernet import Fernet
import os

class GitHubClient:
    """GitHub API client for managing repositories and commits"""
    
    def __init__(self, access_token=None):
        """
        Initialize GitHub client with access token
        
        Args:
            access_token: GitHub personal access token or OAuth token
        """
        self.access_token = access_token
        self.client = GithubAPI(access_token) if access_token else None
        
    def get_user(self):
        """Get authenticated user information"""
        if not self.client:
            raise ValueError("GitHub client not authenticated")
        return self.client.get_user()
    
    def create_repository(self, name, description="", private=True, auto_init=True):
        """
        Create a new GitHub repository
        
        Args:
            name: Repository name
            description: Repository description
            private: Whether the repository should be private
            auto_init: Initialize with README
            
        Returns:
            Repository object
        """
        try:
            user = self.get_user()
            repo = user.create_repo(
                name=name,
                description=description,
                private=private,
                auto_init=auto_init
            )
            return {
                'id': repo.id,
                'name': repo.name,
                'full_name': repo.full_name,
                'html_url': repo.html_url,
                'clone_url': repo.clone_url,
                'default_branch': repo.default_branch,
                'private': repo.private
            }
        except GithubException as e:
            raise Exception(f"Failed to create repository: {e.data.get('message', str(e))}")
    
    def get_repository(self, repo_name):
        """Get repository by name (format: "owner/repo")"""
        try:
            return self.client.get_repo(repo_name)
        except GithubException as e:
            raise Exception(f"Failed to get repository: {e.data.get('message', str(e))}")
    
    def create_or_update_file(self, repo_name, file_path, content, commit_message, branch="main"):
        """
        Create or update a file (checks if exists first)
        
        Args:
            repo_name: Repository name (format: "owner/repo")
            file_path: Path to file in repository
            content: File content
            commit_message: Commit message
            branch: Branch name
            
        Returns:
            Commit information
        """
        try:
            repo = self.get_repository(repo_name)
            try:
                # Try to get the file (will raise exception if doesn't exist)
                file = repo.get_contents(file_path, ref=branch)
                # File exists, update it
                result = repo.update_file(
                    path=file_path,
                    message=commit_message,
                    content=content,
                    sha=file.sha,
                    branch=branch
                )
            except GithubException:
                # File doesn't exist, create it
                result = repo.create_file(
                    path=file_path,
                    message=commit_message,
                    content=content,
                    branch=branch
                )
            return {
                'commit': result['commit'].sha,
                'content': result['content'].path
            }
        except Exception as e:
            raise Exception(f"Failed to create/update file: {str(e)}")
    
    def list_repositories(self):
        """List all repositories for authenticated user"""
        try:
            user = self.get_user()
            repos = user.get_repos()
            return [{
                'id': repo.id,
                'name': repo.name,
                'full_name': repo.full_name,
                'html_url': repo.html_url,
                'private': repo.private,
                'description': repo.description
            } for repo in repos]
        except GithubException as e:
            raise Exception(f"Failed to list repositories: {e.data.get('message', str(e))}")


class TokenEncryption:
    """Utility class for encrypting/decrypting GitHub tokens"""
    
    @staticmethod
    def get_encryption_key():
        """Get or generate encryption key"""
        key = os.environ.get('GITHUB_TOKEN_ENCRYPTION_KEY')
        if not key:
            key = Fernet.generate_key().decode()
            print(f"Add to .env: GITHUB_TOKEN_ENCRYPTION_KEY={key}")
        return key.encode() if isinstance(key, str) else key
    
    @staticmethod
    def encrypt_token(token):
        """Encrypt GitHub access token"""
        key = TokenEncryption.get_encryption_key()
        f = Fernet(key)
        return f.encrypt(token.encode()).decode()
    
    @staticmethod
    def decrypt_token(encrypted_token):
        """Decrypt GitHub access token"""
        key = TokenEncryption.get_encryption_key()
        f = Fernet(key)
        return f.decrypt(encrypted_token.encode()).decode()
```

### 4. Models

```python
# github_integration/models.py
from django.db import models
from django.contrib.auth.models import User
from projects.models import Project
from .client import TokenEncryption


class GitHubAccount(models.Model):
    """Store GitHub account information for users"""
    
    user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='github_account')
    github_id = models.BigIntegerField(unique=True)  # Use BigIntegerField for GitHub IDs
    username = models.CharField(max_length=255)
    email = models.EmailField(blank=True, null=True)  # Allow null for privacy
    avatar_url = models.URLField(blank=True)
    access_token_encrypted = models.TextField()  # Store encrypted token
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        db_table = 'github_accounts'
    
    def __str__(self):
        return f"{self.username} (GitHub)"
    
    def set_access_token(self, token):
        """Encrypt and store access token"""
        self.access_token_encrypted = TokenEncryption.encrypt_token(token)
    
    def get_access_token(self):
        """Decrypt and return access token"""
        return TokenEncryption.decrypt_token(self.access_token_encrypted)


class GitHubRepository(models.Model):
    """Store GitHub repository information linked to projects"""
    
    project = models.OneToOneField(Project, on_delete=models.CASCADE, related_name='github_repository')
    github_account = models.ForeignKey(GitHubAccount, on_delete=models.CASCADE, related_name='repositories')
    repo_id = models.BigIntegerField()  # Use BigIntegerField for GitHub repo IDs
    name = models.CharField(max_length=255)
    full_name = models.CharField(max_length=255)  # owner/repo format
    html_url = models.URLField()
    clone_url = models.URLField()
    default_branch = models.CharField(max_length=100, default='main')
    is_private = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        db_table = 'github_repositories'
        unique_together = ['github_account', 'repo_id']
    
    def __str__(self):
        return self.full_name


class GitHubCommit(models.Model):
    """Track commits made to GitHub repositories"""
    
    repository = models.ForeignKey(GitHubRepository, on_delete=models.CASCADE, related_name='commits')
    sha = models.CharField(max_length=40)
    message = models.TextField()
    author = models.CharField(max_length=255)
    branch = models.CharField(max_length=100, default='main')
    file_path = models.CharField(max_length=500, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        db_table = 'github_commits'
        ordering = ['-created_at']
    
    def __str__(self):
        return f"{self.sha[:7]} - {self.message[:50]}"
```

### 5. Signals for Auto-Token Storage

```python
# github_integration/signals.py
from django.dispatch import receiver
from allauth.socialaccount.signals import social_account_added
from .models import GitHubAccount

@receiver(social_account_added)
def save_github_account(sender, request, sociallogin, **kwargs):
    """Automatically save GitHub account info when user connects"""
    if sociallogin.account.provider == 'github':
        user = sociallogin.user
        extra_data = sociallogin.account.extra_data
        
        github_account, created = GitHubAccount.objects.get_or_create(
            user=user,
            defaults={
                'github_id': extra_data.get('id'),
                'username': extra_data.get('login'),
                'email': extra_data.get('email', ''),
                'avatar_url': extra_data.get('avatar_url', ''),
            }
        )
        
        # Store encrypted access token
        token = sociallogin.token.token
        github_account.set_access_token(token)
        github_account.save()
```

### 6. Apps Configuration

```python
# github_integration/apps.py
from django.apps import AppConfig

class GithubIntegrationConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'github_integration'
    
    def ready(self):
        import github_integration.signals  # Import signals
```

### 7. Serializers

```python
# github_integration/serializers.py
from rest_framework import serializers
from .models import GitHubAccount, GitHubRepository, GitHubCommit


class GitHubAccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = GitHubAccount
        fields = ['id', 'github_id', 'username', 'email', 'avatar_url', 'created_at']
        read_only_fields = ['id', 'github_id', 'created_at']


class GitHubRepositorySerializer(serializers.ModelSerializer):
    github_account_username = serializers.CharField(source='github_account.username', read_only=True)
    project_name = serializers.CharField(source='project.name', read_only=True)
    
    class Meta:
        model = GitHubRepository
        fields = [
            'id', 'project', 'github_account', 'github_account_username',
            'project_name', 'repo_id', 'name', 'full_name', 'html_url',
            'clone_url', 'default_branch', 'is_private', 'created_at'
        ]
        read_only_fields = ['id', 'repo_id', 'created_at']


class GitHubCommitSerializer(serializers.ModelSerializer):
    repository_name = serializers.CharField(source='repository.full_name', read_only=True)
    sha_short = serializers.SerializerMethodField()
    
    class Meta:
        model = GitHubCommit
        fields = [
            'id', 'repository', 'repository_name', 'sha', 'sha_short',
            'message', 'author', 'branch', 'file_path', 'created_at'
        ]
        read_only_fields = ['id', 'created_at']
    
    def get_sha_short(self, obj):
        return obj.sha[:7]


class RepositoryCreateSerializer(serializers.Serializer):
    """Serializer for creating a new GitHub repository"""
    project_id = serializers.IntegerField()
    name = serializers.CharField(max_length=255)
    description = serializers.CharField(required=False, allow_blank=True)
    private = serializers.BooleanField(default=True)
    auto_init = serializers.BooleanField(default=True)


class CommitFileSerializer(serializers.Serializer):
    """Serializer for committing a file to GitHub"""
    file_path = serializers.CharField(max_length=500)
    content = serializers.CharField()
    commit_message = serializers.CharField()
    branch = serializers.CharField(default='main')
```

### 8. API Views

```python
# github_integration/views.py
from rest_framework import viewsets, status
from rest_framework.decorators import action
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated
from django.shortcuts import get_object_or_404
from django.conf import settings
import logging

logger = logging.getLogger(__name__)

from .models import GitHubAccount, GitHubRepository, GitHubCommit
from .serializers import (
    GitHubAccountSerializer,
    GitHubRepositorySerializer,
    GitHubCommitSerializer,
    RepositoryCreateSerializer,
    CommitFileSerializer
)
from .client import GitHubClient
from projects.models import Project


class GitHubAccountViewSet(viewsets.ReadOnlyModelViewSet):
    """ViewSet for GitHub accounts"""
    serializer_class = GitHubAccountSerializer
    permission_classes = [IsAuthenticated]
    
    def get_queryset(self):
        return GitHubAccount.objects.filter(user=self.request.user)


class GitHubRepositoryViewSet(viewsets.ModelViewSet):
    """ViewSet for GitHub repositories"""
    serializer_class = GitHubRepositorySerializer
    permission_classes = [IsAuthenticated]
    
    def get_queryset(self):
        user = self.request.user
        return GitHubRepository.objects.filter(github_account__user=user)
    
    @action(detail=False, methods=['post'], url_path='create-repository')
    def create_repository(self, request):
        """Create a new GitHub repository for a project"""
        serializer = RepositoryCreateSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        
        # Get project and GitHub account
        project = get_object_or_404(Project, id=serializer.validated_data['project_id'])
        github_account = get_object_or_404(GitHubAccount, user=request.user)
        
        # Check if repository already exists for this project
        if GitHubRepository.objects.filter(project=project).exists():
            return Response(
                {'error': 'Repository already exists for this project'},
                status=status.HTTP_400_BAD_REQUEST
            )
        
        try:
            # Create repository using GitHub API
            client = GitHubClient(github_account.get_access_token())
            repo_data = client.create_repository(
                name=serializer.validated_data['name'],
                description=serializer.validated_data.get('description', ''),
                private=serializer.validated_data.get('private', True),
                auto_init=serializer.validated_data.get('auto_init', True)
            )
            
            # Save repository information
            repository = GitHubRepository.objects.create(
                project=project,
                github_account=github_account,
                repo_id=repo_data['id'],
                name=repo_data['name'],
                full_name=repo_data['full_name'],
                html_url=repo_data['html_url'],
                clone_url=repo_data['clone_url'],
                default_branch=repo_data['default_branch'],
                is_private=repo_data['private']
            )
            
            return Response(
                GitHubRepositorySerializer(repository).data,
                status=status.HTTP_201_CREATED
            )
        except Exception as e:
            import traceback
            error_msg = str(e) if str(e) else repr(e)
            error_trace = traceback.format_exc()
            logger.error(f"Error creating GitHub repository: {error_msg}\n{error_trace}")
            return Response(
                {'error': error_msg, 'detail': error_trace if settings.DEBUG else 'Internal server error'},
                status=status.HTTP_500_INTERNAL_SERVER_ERROR
            )
    
    @action(detail=True, methods=['post'])
    def commit_file(self, request, pk=None):
        """Commit a file to the repository"""
        repository = self.get_object()
        serializer = CommitFileSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        
        try:
            # Get GitHub client
            github_account = repository.github_account
            client = GitHubClient(github_account.get_access_token())
            
            # Commit file
            commit_data = client.create_or_update_file(
                repo_name=repository.full_name,
                file_path=serializer.validated_data['file_path'],
                content=serializer.validated_data['content'],
                commit_message=serializer.validated_data['commit_message'],
                branch=serializer.validated_data.get('branch', 'main')
            )
            
            # Save commit information
            commit = GitHubCommit.objects.create(
                repository=repository,
                sha=commit_data['commit'],
                message=serializer.validated_data['commit_message'],
                author=github_account.username,
                branch=serializer.validated_data.get('branch', 'main'),
                file_path=serializer.validated_data['file_path']
            )
            
            return Response(
                GitHubCommitSerializer(commit).data,
                status=status.HTTP_201_CREATED
            )
        except Exception as e:
            return Response(
                {'error': str(e)},
                status=status.HTTP_500_INTERNAL_SERVER_ERROR
            )
    
    @action(detail=True, methods=['get'])
    def commits(self, request, pk=None):
        """Get all commits for a repository"""
        repository = self.get_object()
        commits = GitHubCommit.objects.filter(repository=repository)
        serializer = GitHubCommitSerializer(commits, many=True)
        return Response(serializer.data)


class GitHubCommitViewSet(viewsets.ReadOnlyModelViewSet):
    """ViewSet for GitHub commits"""
    serializer_class = GitHubCommitSerializer
    permission_classes = [IsAuthenticated]
    
    def get_queryset(self):
        user = self.request.user
        return GitHubCommit.objects.filter(
            repository__github_account__user=user
        )
```

### 9. URL Configuration

```python
# github_integration/urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import GitHubAccountViewSet, GitHubRepositoryViewSet, GitHubCommitViewSet

router = DefaultRouter()
router.register(r'accounts', GitHubAccountViewSet, basename='github-account')
router.register(r'repositories', GitHubRepositoryViewSet, basename='github-repository')
router.register(r'commits', GitHubCommitViewSet, basename='github-commit')

urlpatterns = [
    path('', include(router.urls)),
]

# config/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('accounts/', include('allauth.urls')),  # GitHub OAuth
    path('api/github/', include('github_integration.urls')),
    # ... other urls
]
```

---

## Frontend Implementation

### 1. API Client

```typescript
// lib/api.ts
const API_BASE_URL = process.env.NEXT_PUBLIC_API_URL || 'http://localhost:8000/api';

class ApiClient {
  private baseUrl: string;

  constructor(baseUrl: string = API_BASE_URL) {
    this.baseUrl = baseUrl;
  }

  private getCSRFToken(): string | null {
    // Get CSRF token from cookie
    const name = 'csrftoken';
    let cookieValue: string | null = null;
    if (typeof document !== 'undefined' && document.cookie) {
      const cookies = document.cookie.split(';');
      for (let i = 0; i < cookies.length; i++) {
        const cookie = cookies[i].trim();
        if (cookie.substring(0, name.length + 1) === (name + '=')) {
          cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
          break;
        }
      }
    }
    return cookieValue;
  }

  private async request<T>(
    endpoint: string,
    options: RequestInit = {}
  ): Promise<T> {
    const url = `${this.baseUrl}${endpoint}`;
    
    const headers: Record<string, string> = {
      'Content-Type': 'application/json',
      ...(options.headers as Record<string, string>),
    };

    // Add CSRF token for non-GET requests
    const csrfToken = this.getCSRFToken();
    if (csrfToken && options.method && options.method !== 'GET') {
      headers['X-CSRFToken'] = csrfToken;
    }
    
    const config: RequestInit = {
      ...options,
      credentials: 'include', // Include cookies for session authentication
      headers,
    };

    const response = await fetch(url, config);
    
    if (!response.ok) {
      const errorData = await response.json().catch(() => ({}));
      const error: any = new Error(errorData.message || `HTTP error! status: ${response.status}`);
      error.response = { status: response.status, data: errorData };
      throw error;
    }

    return await response.json();
  }

  // GitHub endpoints
  async getGitHubAccounts(): Promise<any[]> {
    return this.request<any[]>('/github/accounts/');
  }

  async getGitHubRepositories(): Promise<any[]> {
    return this.request<any[]>('/github/repositories/');
  }

  async createGitHubRepository(data: {
    project_id: number;
    name: string;
    description?: string;
    private?: boolean;
    auto_init?: boolean;
  }): Promise<any> {
    return this.request<any>('/github/repositories/create-repository/', {
      method: 'POST',
      body: JSON.stringify(data),
    });
  }

  async commitFileToGitHub(repositoryId: string, data: {
    file_path: string;
    content: string;
    commit_message: string;
    branch?: string;
  }): Promise<any> {
    return this.request<any>(`/github/repositories/${repositoryId}/commit_file/`, {
      method: 'POST',
      body: JSON.stringify(data),
    });
  }

  async getRepositoryCommits(repositoryId: string): Promise<any[]> {
    return this.request<any[]>(`/github/repositories/${repositoryId}/commits/`);
  }
}

// Export singleton instance
export const api = new ApiClient();
```

### 2. GitHub Connect Modal Component

```typescript
// components/github/GitHubConnectModal.tsx
'use client';

import { useState } from 'react';
import { api } from '@/lib/api';
import { GitHubRepository } from '@/lib/types';

interface GitHubConnectModalProps {
  isOpen: boolean;
  onClose: () => void;
  projectId: string;
  onSuccess: (repository: GitHubRepository) => void;
}

export default function GitHubConnectModal({
  isOpen,
  onClose,
  projectId,
  onSuccess,
}: GitHubConnectModalProps) {
  const [step, setStep] = useState<'connect' | 'create'>('connect');
  const [repoName, setRepoName] = useState('');
  const [description, setDescription] = useState('');
  const [isPrivate, setIsPrivate] = useState(true);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');

  if (!isOpen) return null;

  const handleCreateRepository = async () => {
    if (!repoName.trim()) {
      setError('Repository name is required');
      return;
    }

    setLoading(true);
    setError('');

    try {
      const repository = await api.createGitHubRepository({
        project_id: parseInt(projectId),
        name: repoName,
        description: description || undefined,
        private: isPrivate,
        auto_init: true,
      });

      onSuccess(repository);
      onClose();
    } catch (err: any) {
      setError(err.message || 'Failed to create repository');
    } finally {
      setLoading(false);
    }
  };

  const handleGitHubConnect = () => {
    // For now, show the create repository form
    // OAuth flow is handled by django-allauth
    setStep('create');
  };

  return (
    <div className="fixed inset-0 bg-black/50 flex items-center justify-center z-50 p-4">
      <div className="bg-white rounded-lg shadow-xl max-w-md w-full pixel-border">
        {/* Header */}
        <div className="px-6 py-4 border-b border-gray-200">
          <div className="flex items-center justify-between">
            <h2 className="text-xl font-bold text-gray-900 pixel-font">
              🔗 Connect GitHub
            </h2>
            <button
              onClick={onClose}
              className="text-gray-400 hover:text-gray-600 text-2xl leading-none"
            >
              ×
            </button>
          </div>
        </div>

        {/* Content */}
        <div className="px-6 py-4">
          {step === 'connect' ? (
            <div className="space-y-4">
              <p className="text-gray-600">
                Connect your GitHub account to automatically create and manage repositories for your project.
              </p>

              <div className="bg-blue-50 border border-blue-200 rounded-lg p-4">
                <h3 className="font-semibold text-blue-900 mb-2">✨ Features</h3>
                <ul className="text-sm text-blue-800 space-y-1">
                  <li>• Auto-create project repository</li>
                  <li>• Commit code changes automatically</li>
                  <li>• Track development progress</li>
                  <li>• Manage branches and PRs</li>
                </ul>
              </div>

              {error && (
                <div className="bg-red-50 border border-red-200 rounded-lg p-3">
                  <p className="text-sm text-red-800">{error}</p>
                </div>
              )}

              <button
                onClick={handleGitHubConnect}
                disabled={loading}
                className="w-full bg-gray-900 text-white py-3 rounded-lg hover:bg-gray-800 
                         transition-colors font-semibold flex items-center justify-center gap-2
                         disabled:opacity-50 disabled:cursor-not-allowed"
              >
                <svg className="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                  <path fillRule="evenodd" d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z" clipRule="evenodd" />
                </svg>
                {loading ? 'Connecting...' : 'Connect with GitHub'}
              </button>
            </div>
          ) : (
            <div className="space-y-4">
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-2">
                  Repository Name *
                </label>
                <input
                  type="text"
                  value={repoName}
                  onChange={(e) => setRepoName(e.target.value)}
                  placeholder="my-awesome-project"
                  className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 
                           focus:ring-blue-500 focus:border-transparent"
                />
              </div>

              <div>
                <label className="block text-sm font-medium text-gray-700 mb-2">
                  Description
                </label>
                <textarea
                  value={description}
                  onChange={(e) => setDescription(e.target.value)}
                  placeholder="A brief description of your project..."
                  rows={3}
                  className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 
                           focus:ring-blue-500 focus:border-transparent resize-none"
                />
              </div>

              <div className="flex items-center gap-2">
                <input
                  type="checkbox"
                  id="private"
                  checked={isPrivate}
                  onChange={(e) => setIsPrivate(e.target.checked)}
                  className="w-4 h-4 text-blue-600 border-gray-300 rounded focus:ring-blue-500"
                />
                <label htmlFor="private" className="text-sm text-gray-700">
                  Make repository private
                </label>
              </div>

              {error && (
                <div className="bg-red-50 border border-red-200 rounded-lg p-3">
                  <p className="text-sm text-red-800">{error}</p>
                </div>
              )}

              <div className="flex gap-3">
                <button
                  onClick={() => setStep('connect')}
                  disabled={loading}
                  className="flex-1 px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50 
                           transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
                >
                  Back
                </button>
                <button
                  onClick={handleCreateRepository}
                  disabled={loading || !repoName.trim()}
                  className="flex-1 bg-blue-600 text-white py-2 rounded-lg hover:bg-blue-700 
                           transition-colors font-semibold disabled:opacity-50 disabled:cursor-not-allowed"
                >
                  {loading ? 'Creating...' : 'Create Repository'}
                </button>
              </div>
            </div>
          )}
        </div>
      </div>
    </div>
  );
}
```

### 3. OAuth Callback Handler

```typescript
// app/auth/callback/page.tsx
'use client';

import { useEffect } from 'react';
import { useRouter } from 'next/navigation';

export default function AuthCallback() {
  const router = useRouter();

  useEffect(() => {
    // OAuth callback is handled by django-allauth
    // After successful authentication, redirect to dashboard
    const timer = setTimeout(() => {
      router.push('/');
    }, 1000);

    return () => clearTimeout(timer);
  }, [router]);

  return (
    <div className="min-h-screen flex items-center justify-center">
      <div className="text-center">
        <h1 className="text-2xl font-bold mb-4">Authenticating...</h1>
        <p className="text-gray-600">Please wait while we connect your GitHub account.</p>
      </div>
    </div>
  );
}
```

---

## Setup Instructions

### 1. GitHub OAuth App Setup

1. Go to https://github.com/settings/developers
2. Click "New OAuth App"
3. Fill in:
   - **Application name**: Your App Name
   - **Homepage URL**: `http://localhost:3000`
   - **Authorization callback URL**: `http://localhost:8000/accounts/github/login/callback/`
4. Copy Client ID and Client Secret

### 2. Django Admin Configuration

```bash
# Create superuser
python manage.py createsuperuser

# Run migrations
python manage.py makemigrations
python manage.py migrate

# Access admin at http://localhost:8000/admin/
```

In Django Admin:
1. Go to **Sites** → Edit site (ID=1) → Set domain to `localhost:8000` (NO http://)
2. Go to **Social applications** → Add social application:
   - Provider: GitHub
   - Name: GitHub OAuth
   - Client id: [Your Client ID]
   - Secret key: [Your Client Secret]
   - Sites: Select `localhost:8000` and move to "Chosen sites"

### 3. Environment Variables

```bash
# backend/.env
GITHUB_TOKEN_ENCRYPTION_KEY=<generate with: python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())">
SECRET_KEY=your-django-secret-key
DEBUG=True
```

```bash
# frontend/.env.local
NEXT_PUBLIC_API_URL=http://localhost:8000/api
```

---

## Common Issues & Solutions

### Issue: "SocialApp.DoesNotExist"
**Solution**: Ensure Site domain is `localhost:8000` (NO `http://`) and Social App is linked to correct site.

```bash
# Quick fix command
python manage.py shell -c "from django.contrib.sites.models import Site; site = Site.objects.get(id=1); site.domain = 'localhost:8000'; site.save(); print('Fixed!')"
```

### Issue: "Redirect URI mismatch"
**Solution**: Verify GitHub OAuth App callback URL is exactly: `http://localhost:8000/accounts/github/login/callback/`

### Issue: Token encryption errors
**Solution**: Generate and set `GITHUB_TOKEN_ENCRYPTION_KEY` in `.env`

```bash
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
```

---

## Production Deployment

1. **Create production GitHub OAuth App** with production URLs
2. **Update Django settings**:
   - Set `DEBUG = False`
   - Update `ALLOWED_HOSTS`
   - Set `SESSION_COOKIE_SECURE = True`
   - Set `CSRF_COOKIE_SECURE = True`
3. **Update Site domain** in Django Admin to production domain (e.g., `yourdomain.com`)
4. **Use HTTPS** for all URLs

---

## Key Features Implemented

✅ GitHub OAuth authentication with django-allauth  
✅ Automatic token encryption and storage via signals  
✅ Repository creation via PyGithub API  
✅ File commit functionality  
✅ Commit history tracking  
✅ Frontend modal for repository creation  
✅ Secure token management with Fernet encryption  
✅ Error handling and validation  
✅ CSRF protection for API requests  

---

## Critical Implementation Details

1. **Use `BigIntegerField` for GitHub IDs** - GitHub IDs can exceed 32-bit integer limits
2. **Field name is `access_token_encrypted`** - Not `encrypted_access_token`
3. **Email field allows null** - Some users may not have public email
4. **Signal must be imported in apps.py** - Add `def ready()` method
5. **SOCIALACCOUNT_STORE_TOKENS = True** - Required to store OAuth tokens
6. **Site domain has NO protocol** - Use `localhost:8000`, not `http://localhost:8000`
7. **CSRF token required for POST requests** - Frontend must include X-CSRFToken header
8. **Credentials: 'include'** - Required for session cookies in fetch requests

---

## Testing

```bash
# Backend tests
python manage.py test github_integration

# Test OAuth flow
1. Visit http://localhost:3000
2. Click "Login with GitHub"
3. Authorize application
4. Verify redirect to callback page
5. Check Django Admin for GitHubAccount entry

# Test repository creation
1. Create a project
2. Click "Connect GitHub"
3. Enter repository details
4. Verify repository created on GitHub
5. Check GitHubRepository model in admin
```

---

## References

- [django-allauth Documentation](https://django-allauth.readthedocs.io/)
- [PyGithub Documentation](https://pygithub.readthedocs.io/)
- [GitHub OAuth Documentation](https://docs.github.com/en/developers/apps/building-oauth-apps)
- [GitHub REST API](https://docs.github.com/en/rest)

---

Made with ❤️ by Bob

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiden-kwak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
