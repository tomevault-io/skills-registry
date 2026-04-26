---
name: course-builder
description: Create professional online courses with modules, quizzes, and certificates. Use this skill when users want to create, develop, or structure an online course, training program, or educational content. Triggers: online course, create course, course builder, curriculum, modules, lessons, quizzes, certificate, training program, educational content, criar curso, curso online, montar um curso. Use when this capability is needed.
metadata:
  author: lkb-99
---

# Course Builder: Developing Complete Online Courses

## Overview

The Course Builder skill is a powerful tool designed to assist educators, trainers, and content creators in developing comprehensive online courses from scratch. This skill streamlines the entire course creation process, from structuring the curriculum and generating content to creating assessments and issuing certificates. By leveraging a suite of integrated tools, it enables users to build engaging and effective learning experiences with minimal friction. Whether you are a seasoned instructional designer or a subject matter expert new to online education, this skill provides the framework and automation needed to bring your educational vision to life.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: online course, create course, build course, course builder, curriculum, modules, lessons, quizzes, certificate, training program, educational content, course outline, instructional design, e-learning
- Palavras-chave (PT): criar curso, curso online, montar um curso, desenvolver curso, estrutura de curso, material didático, curso EAD, videoaulas, criar treinamento
- Phrases: "create an online course", "build a training program", "develop a curriculum", "I want to make a course about...", "how to structure a course"
- Frases (PT): "quero criar um curso online", "como montar um curso de", "preciso desenvolver um treinamento", "criar um material didático"
- Context: Any discussion about creating, structuring, or developing educational or training content from scratch.

**Example user queries that trigger this skill:**
- "I want to create an online course about digital marketing."
- "Can you help me build a corporate training program for new hires?"
- "Quero montar um curso online sobre finanças pessoais."
- "Preciso de ajuda para estruturar o currículo do meu curso."

## When to Use This Skill

**ALWAYS use this skill when user mentions:**
- Creating a new online course
- Converting existing materials into a course
- Building corporate training programs
- Developing educational content for a specific audience
- Structuring a curriculum or course outline
- Automating content generation for lessons or quizzes
- Generating certificates of completion

This skill is particularly useful in the following scenarios:

- **Developing a new online course:** When you have an idea for a course but need a structured process to develop it.
- **Converting existing materials into a course:** If you have presentations, documents, or videos that you want to package into a formal online course.
- **Creating corporate training programs:** For building internal training modules for employees on various topics, from compliance to new software.
- **Building educational content for a specific audience:** When creating courses for a niche market or a specific learning objective.
- **Automating content generation:** To speed up the process of writing lesson content, creating quiz questions, and generating other course materials.
- **Structuring a curriculum:** When you need help organizing your course into logical modules, lessons, and topics.
- **Generating certificates of completion:** To automatically create and prepare personalized certificates for students who complete the course.
- **Prototyping a course idea:** To quickly create a minimum viable product (MVP) of a course to test with a target audience.

## Core Capabilities

### 1. Curriculum Design and Structuring

The skill helps you outline your course structure, defining modules, lessons, and topics. It can generate a logical flow for your content based on a given subject and learning objectives.

- **Generate Course Outline:** Create a complete hierarchical outline for your course.
- **Define Learning Objectives:** For each module and lesson, define clear and measurable learning outcomes.
- **Content Scaffolding:** Generate placeholder files and directories for each part of your course, creating a clean project structure.

```bash
# Example of creating a course structure
mkdir -p my-awesome-course/module-1/{lessons,quizzes,resources}
mkdir -p my-awesome-course/module-2/{lessons,quizzes,resources}
mkdir -p my-awesome-course/module-3/{lessons,quizzes,resources}
touch my-awesome-course/module-1/lessons/lesson-1.md
touch my-awesome-course/module-1/quizzes/quiz-1.md
```

### 2. Content Generation

Leverage AI to generate high-quality written content for your lessons. Provide a topic or a set of keywords, and the skill will produce detailed explanations, examples, and summaries.

- **Lesson Content Creation:** Generate text for lessons based on a title or a brief description.
- **Code Example Generation:** Create relevant code snippets in various programming languages to illustrate concepts.
- **Scripting for Video Lectures:** Write scripts for video lessons, including narration and on-screen text.

### 3. Quiz and Assessment Creation

Easily create quizzes and assessments to test learners' knowledge. The skill can generate various types of questions based on the lesson content.

- **Multiple-Choice Questions:** Generate a set of multiple-choice questions with correct answers and distractors.
- **True/False Questions:** Create true/false statements based on the course material.
- **Short Answer Questions:** Formulate open-ended questions that require a brief written response.
- **Quiz Answer Keys:** Automatically generate an answer key for each quiz.

**Template for a Quiz File (quiz-1.md):**

```markdown
# Quiz: Introduction to Python

**Instructions:** Choose the best answer for each question.

**1. What is the output of `print(2 ** 3)`?**
    a) 5
    b) 6
    c) 8
    d) 9

**2. Which of the following is a mutable data type in Python?**
    a) Tuple
    b) String
    c) List
    d) Integer

**3. True or False: Python is a statically-typed language.**

---

## Answer Key

1. **c) 8**
2. **c) List**
3. **False**
```

### 4. Certificate Generation

Create professional-looking certificates of completion for your students. The skill provides templates that can be customized with the student's name, the course name, and the completion date.

- **Certificate Templates:** Use pre-built HTML or Markdown templates for certificates.
- **Personalization:** Automatically insert student information into the certificate.
- **PDF Conversion:** Convert the generated certificate into a downloadable PDF format.

**Template for a Certificate (certificate.html):**

```html
<!DOCTYPE html>
<html>
<head>
<title>Certificate of Completion</title>
<style>
    body { font-family: sans-serif; text-align: center; }
    .container { border: 10px solid #787878; width: 800px; margin: 0 auto; padding: 20px; }
    .title { font-size: 50px; font-weight: bold; }
    .subtitle { font-size: 25px; }
    .student-name { font-size: 40px; font-style: italic; margin: 40px 0; }
</style>
</head>
<body>
    <div class="container">
        <div class="title">Certificate of Completion</div>
        <div class="subtitle">This certificate is proudly presented to</div>
        <div class="student-name">[STUDENT_NAME]</div>
        <div class="subtitle">for successfully completing the course</div>
        <div class="title">[COURSE_NAME]</div>
        <div class="subtitle">on [COMPLETION_DATE]</div>
    </div>
</body>
</html>
```

## Step-by-Step Workflow

1.  **Initialize the Course Project:**
    -   Start by creating a main directory for your course.
    -   Use the `Bash` tool to create a structured set of folders for modules, lessons, quizzes, and resources.

2.  **Define the Curriculum:**
    -   Create a `CURRICULUM.md` file.
    -   Outline the course structure, listing all modules and the lessons within each module.
    -   For each lesson, write a brief learning objective.

3.  **Generate Lesson Content:**
    -   For each lesson in your curriculum file, use the `Write` tool to create a new Markdown file (e.g., `module-1/lessons/lesson-1.md`).
    -   Provide the skill with the lesson title and learning objective to generate the content.
    -   Review and edit the generated content to ensure accuracy and clarity. Add your own insights and examples.

4.  **Create Quizzes:**
    -   After creating the content for a module, create a quiz file (e.g., `module-1/quizzes/quiz-1.md`).
    -   Use the skill to generate questions based on the content of the lessons in that module.
    -   Specify the number and type of questions you want (e.g., 5 multiple-choice, 2 true/false).
    -   Include an answer key in a separate section of the file.

5.  **Develop Supporting Resources:**
    -   Use the `Browser` tool to find relevant articles, images, and videos to supplement your course.
    -   Download or link to these resources in a `resources` folder for each module.
    -   Create cheat sheets, glossaries, or other supplementary documents.

6.  **Design the Certificate:**
    -   Create an HTML or Markdown template for the certificate of completion.
    -   Use placeholders like `[STUDENT_NAME]`, `[COURSE_NAME]`, and `[COMPLETION_DATE]` for personalization.

7.  **Package the Course:**
    -   Organize all files into a clean and logical structure.
    -   Create a `README.md` file in the root directory with instructions on how to navigate the course content.
    -   Compress the entire course directory into a ZIP file for easy distribution.

    ```bash
    # Example of zipping the course directory
    zip -r my-awesome-course.zip my-awesome-course
    ```

## Best Practices

-   **Start with the End in Mind:** Before you start creating content, have a clear understanding of who your audience is and what you want them to achieve by the end of the course.
-   **Be Iterative:** Don't try to perfect everything in one go. Create a first draft of your course, get feedback, and then iterate on it.
-   **Mix Content Types:** Keep learners engaged by using a variety of content formats, including text, images, videos, and interactive quizzes.
-   **Review and Refine:** Always review the AI-generated content for accuracy, tone, and style. Add your own voice and expertise to make the content unique.
-   **Keep Lessons Short and Focused:** Break down complex topics into smaller, digestible lessons. A good rule of thumb is to keep video lessons between 5-10 minutes long.
-   **Provide Clear Instructions:** Make sure that all instructions for assignments, quizzes, and activities are clear and easy to understand.
-   **Use a Consistent Structure:** Maintain a consistent file and folder structure throughout the course to make it easy for learners to navigate.

## Examples

### Example 1: Creating a New Python Course for Beginners

1.  **Goal:** Create a 3-module course on Python for beginners.

2.  **Initialization:**
    ```bash
    manus shell exec "mkdir -p python-for-beginners/module-1/{lessons,quizzes} && mkdir -p python-for-beginners/module-2/{lessons,quizzes} && mkdir -p python-for-beginners/module-3/{lessons,quizzes}"
    ```

3.  **Curriculum (`CURRICULUM.md`):**
    ```markdown
    # Python for Beginners Course

    ## Module 1: Introduction to Python
    - Lesson 1: What is Python and Why Use It?
    - Lesson 2: Setting Up Your Python Environment
    - Lesson 3: Basic Syntax and Data Types

    ## Module 2: Control Flow and Functions
    - Lesson 1: Conditional Statements (if, elif, else)
    - Lesson 2: Loops (for, while)
    - Lesson 3: Defining and Calling Functions

    ## Module 3: Data Structures
    - Lesson 1: Working with Lists
    - Lesson 2: Understanding Tuples and Dictionaries
    - Lesson 3: Introduction to Sets
    ```

4.  **Generate Content for Module 1, Lesson 1:**
    -   Use the `Write` tool to create `python-for-beginners/module-1/lessons/lesson-1.md`.
    -   Prompt: "Generate content for a lesson titled 'What is Python and Why Use It?'. The learning objective is to explain the history, philosophy, and common use cases of Python."

5.  **Create a Quiz for Module 1:**
    -   Use the `Write` tool to create `python-for-beginners/module-1/quizzes/quiz-1.md`.
    -   Prompt: "Generate a 5-question multiple-choice quiz based on the content of the 'Introduction to Python' module. Include an answer key."

### Example 2: Generating a Certificate

1.  **Create Template:**
    -   Create a file named `certificate_template.html` with the HTML template provided in the 'Core Capabilities' section.

2.  **Personalize:**
    -   Use the `Edit` tool to replace the placeholders.
    ```python
    # Fictional tool usage for demonstration
    file.edit(
        path='certificate.html',
        edits=[
            {'find': '[STUDENT_NAME]', 'replace': 'Jane Doe'},
            {'find': '[COURSE_NAME]', 'replace': 'Python for Beginners'},
            {'find': '[COMPLETION_DATE]', 'replace': 'February 2, 2026'}
        ]
    )
    ```

3.  **Convert to PDF (using a hypothetical tool or external library):**
    ```bash
    # This might require installing a tool like 'wkhtmltopdf'
    wkhtmltopdf certificate.html jane_doe_certificate.pdf
    ```

## References

-   [The ADDIE Model: A Comprehensive Guide for Instructional Designers](https://www.instructionaldesigncentral.com/addie-model)
-   [Bloom's Taxonomy of Learning Domains](https://cft.vanderbilt.edu/guides-sub-pages/blooms-taxonomy/)
-   [Gagné's Nine Events of Instruction](https://www.cmu.edu/teaching/designteach/design/instructionaldesign/gagnes.html)
-   [Awesome Instructional Design on GitHub](https://github.com/pmsi-alignalytics/awesome-instructional-design)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
