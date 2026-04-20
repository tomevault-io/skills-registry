---
name: zmp-sdk-integration
description: Integrates zmp-sdk APIs for Zalo Mini App. Use when asked to implement user auth, storage, routing, media, device features, Zalo sharing, or any zmp-sdk functionality.
metadata:
  author: tienchinh21
---

# ZMP SDK Integration

Specialized skill for integrating zmp-sdk APIs in Zalo Mini App projects.

## When to Use

- "Lấy thông tin user / access token / số điện thoại"
- "Lắng nghe sự kiện AppPaused/AppResumed"
- "Dùng storage / routing / gửi data"
- "Chọn ảnh / camera / download file"
- "Share / mở chat / follow OA"
- "Cấu hình navigation bar / hiển thị toast"

## Core Principle: Wrapper Layer

**Never call zmp-sdk directly in UI components.** Always create abstraction layer:

```
UI Components → Custom Hooks → SDK Wrappers → zmp-sdk
```

## SDK Installation

```bash
npm install zmp-sdk
```

## API Reference by Category

### 1. User & Authentication

| API | Purpose | Requires Permission |
|-----|---------|---------------------|
| `authorize` | Request permissions | No |
| `getUserID` | Get user's Zalo ID | No |
| `getUserInfo` | Get name, avatar | Yes (scope: USER_INFO) |
| `getAccessToken` | Get auth token | No |
| `getPhoneNumber` | Get phone number | Yes (scope: USER_PHONE) |
| `getSetting` | Check granted permissions | No |

**Implementation Pattern:**

```typescript
// lib/zmp/user.ts
import { authorize, getUserID, getUserInfo, getAccessToken, getPhoneNumber, getSetting } from 'zmp-sdk';

export interface ZmpUser {
  id: string;
  name: string;
  avatar: string;
}

export const zmpUserService = {
  async authorize(scopes: string[]): Promise<boolean> {
    try {
      await authorize({ scopes });
      return true;
    } catch (error) {
      console.error('Authorization failed:', error);
      return false;
    }
  },

  async getUserId(): Promise<string | null> {
    try {
      const { userID } = await getUserID();
      return userID;
    } catch (error) {
      console.error('Failed to get user ID:', error);
      return null;
    }
  },

  async getUser(): Promise<ZmpUser | null> {
    try {
      const { userInfo } = await getUserInfo();
      return {
        id: userInfo.id,
        name: userInfo.name,
        avatar: userInfo.avatar,
      };
    } catch (error) {
      console.error('Failed to get user info:', error);
      return null;
    }
  },

  async getAccessToken(): Promise<string | null> {
    try {
      const { accessToken } = await getAccessToken();
      return accessToken;
    } catch (error) {
      console.error('Failed to get access token:', error);
      return null;
    }
  },

  async getPhoneNumber(): Promise<string | null> {
    try {
      const { number } = await getPhoneNumber();
      return number;
    } catch (error) {
      console.error('Failed to get phone number:', error);
      return null;
    }
  },

  async checkPermissions(): Promise<Record<string, boolean>> {
    try {
      const settings = await getSetting();
      return settings.authSetting || {};
    } catch (error) {
      console.error('Failed to get settings:', error);
      return {};
    }
  },
};
```

**Hook:**

```typescript
// hooks/useZmpUser.ts
import { useState, useEffect } from 'react';
import { zmpUserService, ZmpUser } from '../lib/zmp/user';

export const useZmpUser = () => {
  const [user, setUser] = useState<ZmpUser | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        const userData = await zmpUserService.getUser();
        setUser(userData);
      } catch (err) {
        setError(err as Error);
      } finally {
        setLoading(false);
      }
    };
    fetchUser();
  }, []);

  return { user, loading, error };
};
```

### 2. Events (Lifecycle)

| Event | When Triggered |
|-------|---------------|
| `AppPaused` | App goes to background |
| `AppResumed` | App returns to foreground |
| `NetworkChanged` | Network connection changes |
| `OnDataCallback` | Receives data from another Mini App |
| `OpenApp` | App reopened from background |

**CRITICAL: Always cleanup event listeners!**

```typescript
// hooks/useAppLifecycle.ts
import { useEffect, useCallback } from 'react';
import { on, off } from 'zmp-sdk';

export const useAppLifecycle = (
  onPause?: () => void,
  onResume?: () => void
) => {
  const handlePause = useCallback(() => {
    console.log('App paused');
    onPause?.();
  }, [onPause]);

  const handleResume = useCallback(() => {
    console.log('App resumed');
    onResume?.();
  }, [onResume]);

  useEffect(() => {
    on('AppPaused', handlePause);
    on('AppResumed', handleResume);

    // IMPORTANT: Cleanup on unmount
    return () => {
      off('AppPaused', handlePause);
      off('AppResumed', handleResume);
    };
  }, [handlePause, handleResume]);
};
```

**Network Status Hook:**

```typescript
// hooks/useNetworkStatus.ts
import { useState, useEffect, useCallback } from 'react';
import { on, off, getNetworkType } from 'zmp-sdk';

type NetworkType = 'wifi' | '2g' | '3g' | '4g' | '5g' | 'unknown' | 'none';

export const useNetworkStatus = () => {
  const [networkType, setNetworkType] = useState<NetworkType>('unknown');
  const [isConnected, setIsConnected] = useState(true);

  const updateNetworkStatus = useCallback(async () => {
    try {
      const { networkType: type } = await getNetworkType();
      setNetworkType(type as NetworkType);
      setIsConnected(type !== 'none');
    } catch (error) {
      console.error('Failed to get network type:', error);
    }
  }, []);

  useEffect(() => {
    updateNetworkStatus();

    const handleNetworkChange = ({ isConnected: connected }: { isConnected: boolean }) => {
      setIsConnected(connected);
      updateNetworkStatus();
    };

    on('NetworkChanged', handleNetworkChange);
    return () => off('NetworkChanged', handleNetworkChange);
  }, [updateNetworkStatus]);

  return { networkType, isConnected };
};
```

### 3. Storage (Synchronous)

| API | Purpose |
|-----|---------|
| `setItem` | Save data |
| `getItem` | Retrieve data |
| `removeItem` | Delete data |
| `clear` | Clear all data |
| `getStorageInfo` | Get storage info |

```typescript
// lib/zmp/storage.ts
import { setItem, getItem, removeItem, clear } from 'zmp-sdk';

export const zmpStorage = {
  set<T>(key: string, value: T): void {
    try {
      setItem({ key, value: JSON.stringify(value) });
    } catch (error) {
      console.error('Storage set failed:', error);
    }
  },

  get<T>(key: string, defaultValue: T): T {
    try {
      const result = getItem({ key });
      if (result === null || result === undefined) return defaultValue;
      return JSON.parse(result as string) as T;
    } catch (error) {
      console.error('Storage get failed:', error);
      return defaultValue;
    }
  },

  remove(key: string): void {
    try {
      removeItem({ key });
    } catch (error) {
      console.error('Storage remove failed:', error);
    }
  },

  clear(): void {
    try {
      clear();
    } catch (error) {
      console.error('Storage clear failed:', error);
    }
  },
};
```

**Hook:**

```typescript
// hooks/useZmpStorage.ts
import { useState, useCallback } from 'react';
import { zmpStorage } from '../lib/zmp/storage';

export function useZmpStorage<T>(key: string, initialValue: T) {
  const [storedValue, setStoredValue] = useState<T>(() => {
    return zmpStorage.get(key, initialValue);
  });

  const setValue = useCallback((value: T | ((prev: T) => T)) => {
    const valueToStore = value instanceof Function ? value(storedValue) : value;
    setStoredValue(valueToStore);
    zmpStorage.set(key, valueToStore);
  }, [key, storedValue]);

  const removeValue = useCallback(() => {
    setStoredValue(initialValue);
    zmpStorage.remove(key);
  }, [key, initialValue]);

  return [storedValue, setValue, removeValue] as const;
}
```

### 4. Routing

| API | Purpose |
|-----|---------|
| `closeApp` | Close Mini App |
| `openMiniApp` | Open another Mini App |
| `openWebview` | Open webview |
| `sendDataToPreviousMiniApp` | Send data back |
| `getRouteParams` | Get route parameters |

```typescript
// lib/zmp/routing.ts
import { closeApp, openMiniApp, openWebview, sendDataToPreviousMiniApp, getRouteParams } from 'zmp-sdk';

export const zmpRouting = {
  async close(): Promise<void> {
    try {
      await closeApp();
    } catch (error) {
      console.error('Failed to close app:', error);
    }
  },

  async openMiniApp(appId: string, path?: string): Promise<void> {
    try {
      await openMiniApp({ appId, path });
    } catch (error) {
      console.error('Failed to open mini app:', error);
    }
  },

  async openWebview(url: string): Promise<void> {
    try {
      await openWebview({ url });
    } catch (error) {
      console.error('Failed to open webview:', error);
    }
  },

  async sendDataBack(data: Record<string, unknown>): Promise<void> {
    try {
      await sendDataToPreviousMiniApp({ data });
    } catch (error) {
      console.error('Failed to send data:', error);
    }
  },

  getParams<T extends Record<string, string>>(): T {
    try {
      return getRouteParams() as T;
    } catch (error) {
      console.error('Failed to get route params:', error);
      return {} as T;
    }
  },
};
```

### 5. UI Control

| API | Purpose |
|-----|---------|
| `showToast` | Show toast message |
| `closeLoading` | Close splash loading |
| `configAppView` | Configure app view |
| `setNavigationBarColor` | Set nav bar color |
| `setNavigationBarTitle` | Set nav bar title |
| `setNavigationBarLeftButton` | Set left button |
| `hideKeyboard` | Hide keyboard |

```typescript
// lib/zmp/ui.ts
import {
  showToast,
  closeLoading,
  configAppView,
  setNavigationBarColor,
  setNavigationBarTitle,
  hideKeyboard,
} from 'zmp-sdk';

type ToastType = 'success' | 'error' | 'warning' | 'none';

export const zmpUI = {
  toast(message: string, type: ToastType = 'none'): void {
    showToast({ message, type });
  },

  closeLoading(): void {
    closeLoading();
  },

  async configView(options: {
    headerColor?: string;
    statusBarColor?: string;
    headerTextColor?: 'white' | 'black';
    hideAndroidBottomNavigationBar?: boolean;
    hideIOSSafeAreaBottom?: boolean;
  }): Promise<void> {
    try {
      await configAppView(options);
    } catch (error) {
      console.error('Failed to config app view:', error);
    }
  },

  async setNavBarColor(color: string): Promise<void> {
    try {
      await setNavigationBarColor({ color });
    } catch (error) {
      console.error('Failed to set nav bar color:', error);
    }
  },

  async setNavBarTitle(title: string): Promise<void> {
    try {
      await setNavigationBarTitle({ title });
    } catch (error) {
      console.error('Failed to set nav bar title:', error);
    }
  },

  hideKeyboard(): void {
    hideKeyboard();
  },
};
```

### 6. Media & Files

| API | Purpose |
|-----|---------|
| `chooseImage` | Select images |
| `openMediaPicker` | Open media picker |
| `createCameraContext` | Camera control |
| `saveImageToGallery` | Save image |
| `saveVideoToGallery` | Save video |
| `downloadFile` | Download file |
| `openDocument` | Open PDF |

```typescript
// lib/zmp/media.ts
import { chooseImage, openMediaPicker, downloadFile, openDocument, saveImageToGallery } from 'zmp-sdk';

export interface ChooseImageResult {
  filePaths: string[];
  tempFiles: { path: string; size: number }[];
}

export const zmpMedia = {
  async chooseImages(options?: {
    count?: number;
    sourceType?: ('album' | 'camera')[];
  }): Promise<ChooseImageResult | null> {
    try {
      const result = await chooseImage({
        count: options?.count || 9,
        sourceType: options?.sourceType || ['album', 'camera'],
      });
      return result;
    } catch (error) {
      console.error('Failed to choose image:', error);
      return null;
    }
  },

  async openPicker(options?: {
    type?: 'image' | 'video' | 'file';
    maxSelectCount?: number;
  }): Promise<string[] | null> {
    try {
      const result = await openMediaPicker({
        type: options?.type || 'image',
        maxSelectCount: options?.maxSelectCount || 1,
      });
      return result.filePaths || [];
    } catch (error) {
      console.error('Failed to open media picker:', error);
      return null;
    }
  },

  async download(url: string): Promise<string | null> {
    try {
      const { filePath } = await downloadFile({ url });
      return filePath;
    } catch (error) {
      console.error('Failed to download file:', error);
      return null;
    }
  },

  async openPdf(url: string): Promise<void> {
    try {
      await openDocument({ url, type: 'pdf' });
    } catch (error) {
      console.error('Failed to open document:', error);
    }
  },

  async saveToGallery(filePath: string): Promise<boolean> {
    try {
      await saveImageToGallery({ filePath });
      return true;
    } catch (error) {
      console.error('Failed to save image:', error);
      return false;
    }
  },
};
```

### 7. Zalo Integration

| API | Purpose |
|-----|---------|
| `openProfile` | Open user/OA profile |
| `openChat` | Open chat with user/OA |
| `openProfilePicker` | Pick friends |
| `followOA` | Follow OA |
| `unfollowOA` | Unfollow OA |
| `openShareSheet` | Share content |
| `openPostFeed` | Post to feed |
| `createShortcut` | Create home shortcut |
| `viewOAQr` | View OA QR code |
| `minimizeApp` | Minimize app |
| `favoriteApp` | Add to favorites |
| `addRating` | Open rating dialog |

```typescript
// lib/zmp/zalo.ts
import {
  openProfile,
  openChat,
  openProfilePicker,
  followOA,
  unfollowOA,
  openShareSheet,
  openPostFeed,
  createShortcut,
} from 'zmp-sdk';

export const zmpZalo = {
  async openUserProfile(userId: string): Promise<void> {
    try {
      await openProfile({ id: userId, type: 'user' });
    } catch (error) {
      console.error('Failed to open profile:', error);
    }
  },

  async openOAProfile(oaId: string): Promise<void> {
    try {
      await openProfile({ id: oaId, type: 'oa' });
    } catch (error) {
      console.error('Failed to open OA profile:', error);
    }
  },

  async chatWithUser(userId: string, message?: string): Promise<void> {
    try {
      await openChat({ id: userId, type: 'user', message });
    } catch (error) {
      console.error('Failed to open chat:', error);
    }
  },

  async chatWithOA(oaId: string, message?: string): Promise<void> {
    try {
      await openChat({ id: oaId, type: 'oa', message });
    } catch (error) {
      console.error('Failed to open OA chat:', error);
    }
  },

  async pickFriends(maxSelect?: number): Promise<string[] | null> {
    try {
      const result = await openProfilePicker({ maxSelect: maxSelect || 1 });
      return result?.selectedUsers?.map((u) => u.id) || [];
    } catch (error) {
      console.error('Failed to pick friends:', error);
      return null;
    }
  },

  async followOA(oaId: string): Promise<boolean> {
    try {
      await followOA({ id: oaId });
      return true;
    } catch (error) {
      console.error('Failed to follow OA:', error);
      return false;
    }
  },

  async share(data: {
    type: 'link' | 'image' | 'video';
    data: {
      link?: string;
      title?: string;
      description?: string;
      thumb?: string;
    };
  }): Promise<boolean> {
    try {
      await openShareSheet(data);
      return true;
    } catch (error) {
      console.error('Failed to share:', error);
      return false;
    }
  },

  async postToFeed(data: {
    link?: string;
    title?: string;
    description?: string;
    thumb?: string;
  }): Promise<boolean> {
    try {
      await openPostFeed({ ...data });
      return true;
    } catch (error) {
      console.error('Failed to post feed:', error);
      return false;
    }
  },

  async createHomeShortcut(): Promise<boolean> {
    try {
      await createShortcut();
      return true;
    } catch (error) {
      console.error('Failed to create shortcut:', error);
      return false;
    }
  },
};
```

### 8. Device & Permissions

| API | Purpose |
|-----|---------|
| `scanQRCode` | Scan QR code |
| `getNetworkType` | Get network type |
| `vibrate` | Vibrate device |
| `keepScreen` | Keep screen on |
| `openPhone` | Open dialer |
| `openSMS` | Open SMS |
| `getLocation` | Get GPS location |
| `openPermissionSetting` | Open permission settings |
| `requestSendNotification` | Request notification permission |

```typescript
// lib/zmp/device.ts
import {
  scanQRCode,
  vibrate,
  keepScreen,
  openPhone,
  openSMS,
  getLocation,
  openPermissionSetting,
  requestSendNotification,
} from 'zmp-sdk';

export const zmpDevice = {
  async scanQR(): Promise<string | null> {
    try {
      const { content } = await scanQRCode();
      return content;
    } catch (error) {
      console.error('Failed to scan QR:', error);
      return null;
    }
  },

  vibrate(): void {
    vibrate();
  },

  async keepScreenOn(keep: boolean): Promise<void> {
    try {
      await keepScreen({ keepScreenOn: keep });
    } catch (error) {
      console.error('Failed to set keep screen:', error);
    }
  },

  async call(phoneNumber: string): Promise<void> {
    try {
      await openPhone({ phoneNumber });
    } catch (error) {
      console.error('Failed to open phone:', error);
    }
  },

  async sendSMS(phoneNumber: string, body?: string): Promise<void> {
    try {
      await openSMS({ phoneNumber, body });
    } catch (error) {
      console.error('Failed to open SMS:', error);
    }
  },

  async getLocation(): Promise<{ latitude: number; longitude: number } | null> {
    try {
      const { latitude, longitude } = await getLocation();
      return { latitude, longitude };
    } catch (error) {
      console.error('Failed to get location:', error);
      return null;
    }
  },

  async openPermissions(): Promise<void> {
    try {
      await openPermissionSetting();
    } catch (error) {
      console.error('Failed to open permission setting:', error);
    }
  },

  async requestNotificationPermission(): Promise<boolean> {
    try {
      await requestSendNotification();
      return true;
    } catch (error) {
      console.error('Failed to request notification:', error);
      return false;
    }
  },
};
```

## Permission Flow Pattern

For APIs requiring permission:

```typescript
// hooks/usePhoneNumber.ts
import { useState, useCallback } from 'react';
import { zmpUserService } from '../lib/zmp/user';

export const usePhoneNumber = () => {
  const [phone, setPhone] = useState<string | null>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const requestPhone = useCallback(async () => {
    setLoading(true);
    setError(null);

    try {
      // Step 1: Check existing permission
      const settings = await zmpUserService.checkPermissions();
      
      // Step 2: Request if needed
      if (!settings['scope.userPhonenumber']) {
        const granted = await zmpUserService.authorize(['scope.userPhonenumber']);
        if (!granted) {
          setError('Bạn cần cấp quyền để tiếp tục');
          return null;
        }
      }

      // Step 3: Get phone number
      const number = await zmpUserService.getPhoneNumber();
      setPhone(number);
      return number;
    } catch (err) {
      setError('Không thể lấy số điện thoại');
      return null;
    } finally {
      setLoading(false);
    }
  }, []);

  return { phone, loading, error, requestPhone };
};
```

## Error Handling Pattern

Always wrap SDK calls with try-catch and provide typed results:

```typescript
// lib/zmp/result.ts
export type Result<T, E = Error> =
  | { ok: true; data: T }
  | { ok: false; error: E };

export function ok<T>(data: T): Result<T> {
  return { ok: true, data };
}

export function err<E = Error>(error: E): Result<never, E> {
  return { ok: false, error };
}

// Usage in service:
async function getUserSafe(): Promise<Result<ZmpUser>> {
  try {
    const user = await zmpUserService.getUser();
    if (!user) return err(new Error('User not found'));
    return ok(user);
  } catch (error) {
    return err(error as Error);
  }
}
```

## Anti-Patterns to Avoid

1. **Direct SDK calls in components** ❌
   ```tsx
   // BAD
   const MyComponent = () => {
     useEffect(() => {
       getUserInfo().then(setUser);
     }, []);
   };
   ```

2. **Anonymous event handlers** ❌
   ```tsx
   // BAD - can't be removed
   on('AppResumed', () => { ... });
   ```

3. **Missing event cleanup** ❌
   ```tsx
   // BAD - memory leak
   useEffect(() => {
     on('AppResumed', handler);
     // Missing: return () => off('AppResumed', handler);
   }, []);
   ```

4. **No error handling** ❌
   ```tsx
   // BAD
   const user = await getUserInfo(); // May throw!
   ```

5. **Calling sensitive APIs without permission check** ❌
   ```tsx
   // BAD
   const phone = await getPhoneNumber(); // Need permission first!
   ```

## Official Documentation

**IMPORTANT:** When you need specific API details (params, return types, errors):

- Full API Reference: https://miniapp.zaloplatforms.com/documents/api/
- Individual API docs: https://miniapp.zaloplatforms.com/documents/api/{apiName}/

Examples:
- authorize: https://miniapp.zaloplatforms.com/documents/api/authorize/
- getUserInfo: https://miniapp.zaloplatforms.com/documents/api/getUserInfo/
- chooseImage: https://miniapp.zaloplatforms.com/documents/api/chooseImage/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tienchinh21) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
