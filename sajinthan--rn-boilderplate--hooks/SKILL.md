---
name: hooks
description: Generate custom React hooks following established patterns. Use when creating reusable logic, timers, debouncing, API calls, or any stateful logic that needs to be shared across components. Use when this capability is needed.
metadata:
  author: sajinthan
---

# Custom Hooks Generator

Generate React hooks following established patterns.

## Directory Structure

```
src/hooks/
├── index.ts                # Hook exports
├── useTimeout.ts           # Timer hook
├── useInterval.ts          # Recurring timer hook
├── useRateApp.ts          # App review hook
├── useAppState.ts         # App state detection
├── useDebounce.ts         # Debounce hook
├── useThrottle.ts         # Throttle hook
├── usePrevious.ts         # Previous value hook
├── useToggle.ts           # Toggle state hook
├── useKeyboard.ts         # Keyboard detection
├── useColorScheme.ts      # Color scheme detection
└── useThemeColor.ts       # Theme color utilities
```

## Timer Hooks

### useTimeout Hook

Execute a callback after a delay when start condition is true.

```tsx
// src/hooks/useTimeout.ts
import { useEffect } from 'react';

export const useTimeout = (callback: () => void, delay: number, start: boolean) => {
  useEffect(() => {
    if (!start) return;

    const timer = setTimeout(() => {
      callback();
    }, delay);

    return () => {
      clearTimeout(timer);
    };
  }, [callback, delay, start]);
};
```

#### Usage Examples

**Basic Usage**

```tsx
import { useTimeout } from '@/hooks';

const MyComponent = () => {
  const [showMessage, setShowMessage] = useState(false);

  useTimeout(
    () => setShowMessage(true),
    3000, // 3 seconds
    true, // start immediately
  );

  return showMessage ? <Text>Hello!</Text> : null;
};
```

**Conditional Start**

```tsx
const RateAppPrompt = () => {
  const { hasSeenPrompt } = useAppSettings();
  const { requestReview } = useRateApp();

  useTimeout(
    requestReview,
    10 * 60 * 1000, // 10 minutes
    !hasSeenPrompt, // only if not seen before
  );

  return null;
};
```

**Cancel on Condition Change**

```tsx
const AutoSave = () => {
  const [hasChanges, setHasChanges] = useState(false);
  const { save } = useSaveData();

  useTimeout(
    () => {
      save();
      setHasChanges(false);
    },
    5000,
    hasChanges, // restart timer on each change
  );

  return null;
};
```

#### Common Use Cases

- Delayed tooltips
- Auto-save functionality
- Rate app prompts
- Session timeout warnings
- Auto-hide notifications
- Delayed redirects

---

### useInterval Hook

Execute a callback repeatedly at specified intervals.

```tsx
// src/hooks/useInterval.ts
import { useEffect, useRef } from 'react';

export const useInterval = (callback: () => void, delay: number | null, immediate = false) => {
  const savedCallback = useRef(callback);

  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);

  useEffect(() => {
    if (delay === null) return;

    if (immediate) {
      savedCallback.current();
    }

    const id = setInterval(() => {
      savedCallback.current();
    }, delay);

    return () => clearInterval(id);
  }, [delay, immediate]);
};
```

#### Usage Examples

**Polling API**

```tsx
const DataPoller = () => {
  const { fetchData } = useDataStore();

  useInterval(
    fetchData,
    30000, // 30 seconds
    true, // fetch immediately on mount
  );

  return null;
};
```

**Countdown Timer**

```tsx
const CountdownTimer = () => {
  const [seconds, setSeconds] = useState(60);

  useInterval(
    () => {
      setSeconds(s => (s > 0 ? s - 1 : 0));
    },
    1000, // 1 second
    false,
  );

  return <Text>{seconds}s remaining</Text>;
};
```

**Pause/Resume**

```tsx
const AnimationLoop = () => {
  const [isPaused, setIsPaused] = useState(false);

  useInterval(
    animate,
    isPaused ? null : 16, // 60fps, null pauses
    false,
  );

  return <Button onPress={() => setIsPaused(!isPaused)} />;
};
```

#### Common Use Cases

- Auto-refresh data
- Countdown timers
- Animation loops
- Status polling
- Clock displays
- Progress updates

---

## App Integration Hooks

### useRateApp Hook

Request app store review with proper availability checks.

```tsx
// src/hooks/useRateApp.ts
import { useCallback } from 'react';
import * as StoreReview from 'expo-store-review';

export const useRateApp = () => {
  const requestReview = useCallback(async () => {
    try {
      const hasAction = await StoreReview.hasAction();
      const isAvailable = await StoreReview.isAvailableAsync();

      if (isAvailable && hasAction) {
        await StoreReview.requestReview();
        return true;
      }

      return false;
    } catch (error) {
      console.error('Error requesting app review:', error);
      return false;
    }
  }, []);

  return { requestReview };
};
```

#### Usage Example

**Delayed Rate Prompt**

```tsx
import { useRateApp } from '@/hooks';
import { useAppSettingsStore } from '@/store';

const AppInit = () => {
  const { requestReview } = useRateApp();
  const { hasSeenRateAppPrompt, setHasSeenRateAppPrompt } = useAppSettingsStore();

  useTimeout(
    async () => {
      const success = await requestReview();
      if (success) {
        setHasSeenRateAppPrompt(true);
      }
    },
    10 * 60 * 1000, // 10 minutes after app open
    !hasSeenRateAppPrompt,
  );

  return null;
};
```

**After Positive Action**

```tsx
const SuccessScreen = () => {
  const { requestReview } = useRateApp();

  useEffect(() => {
    // Request review after successful action
    requestReview();
  }, []);

  return <Text>Success! 🎉</Text>;
};
```

#### Common Use Cases

- Timed prompts (after X minutes)
- After positive actions (completed task, purchase)
- After reaching milestones
- Periodic reminders (but respecting frequency limits)

---

### useAppState Hook

Detect when app goes to background/foreground.

```tsx
// src/hooks/useAppState.ts
import { useEffect, useRef } from 'react';
import { AppState, AppStateStatus } from 'react-native';

export const useAppState = (onForeground?: () => void, onBackground?: () => void) => {
  const appState = useRef(AppState.currentState);

  useEffect(() => {
    const subscription = AppState.addEventListener('change', (nextAppState: AppStateStatus) => {
      if (appState.current.match(/inactive|background/) && nextAppState === 'active') {
        onForeground?.();
      }

      if (appState.current === 'active' && nextAppState.match(/inactive|background/)) {
        onBackground?.();
      }

      appState.current = nextAppState;
    });

    return () => {
      subscription.remove();
    };
  }, [onForeground, onBackground]);
};
```

#### Usage Examples

**Refresh Data on Foreground**

```tsx
const DataManager = () => {
  const { refreshData } = useDataStore();

  useAppState(
    () => {
      console.log('App came to foreground');
      refreshData();
    },
    () => {
      console.log('App went to background');
    },
  );

  return null;
};
```

**Lock App on Background**

```tsx
const SecurityManager = () => {
  const { setIsAuthenticated } = useBiometricAuthStore();

  useAppState(
    undefined, // no foreground action
    () => {
      setIsAuthenticated(false); // lock on background
    },
  );

  return null;
};
```

**Pause/Resume Timer**

```tsx
const TimerScreen = () => {
  const [isPaused, setIsPaused] = useState(false);

  useAppState(
    () => setIsPaused(false), // resume on foreground
    () => setIsPaused(true), // pause on background
  );

  return <Timer isPaused={isPaused} />;
};
```

#### Common Use Cases

- Refresh data when app returns
- Lock app on background
- Pause/resume timers
- Clear sensitive data
- Analytics tracking
- Re-authenticate user

---

## Value Hooks

### useDebounce Hook

Debounce a value to reduce update frequency.

```tsx
// src/hooks/useDebounce.ts
import { useState, useEffect } from 'react';

export const useDebounce = <T,>(value: T, delay: number): T => {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(timer);
    };
  }, [value, delay]);

  return debouncedValue;
};
```

#### Usage Examples

**Search Input**

```tsx
import { useDebounce } from '@/hooks';

const SearchScreen = () => {
  const [searchText, setSearchText] = useState('');
  const debouncedSearch = useDebounce(searchText, 500);

  useEffect(() => {
    if (debouncedSearch) {
      // Only search after 500ms of no typing
      performSearch(debouncedSearch);
    }
  }, [debouncedSearch]);

  return <Input value={searchText} onChangeText={setSearchText} placeholder="Search..." />;
};
```

**API Call Optimization**

```tsx
const FilteredList = () => {
  const [filters, setFilters] = useState({ category: '', minPrice: 0 });
  const debouncedFilters = useDebounce(filters, 300);

  useEffect(() => {
    fetchFilteredData(debouncedFilters);
  }, [debouncedFilters]);

  return <FilterInputs onChange={setFilters} />;
};
```

**Form Validation**

```tsx
const EmailInput = () => {
  const [email, setEmail] = useState('');
  const debouncedEmail = useDebounce(email, 500);

  useEffect(() => {
    if (debouncedEmail) {
      validateEmail(debouncedEmail);
    }
  }, [debouncedEmail]);

  return <Input value={email} onChangeText={setEmail} />;
};
```

#### Common Use Cases

- Search inputs
- API call optimization
- Form validation
- Auto-save
- Filter controls
- Resize events

---

### useThrottle Hook

Throttle a callback to limit execution frequency.

```tsx
// src/hooks/useThrottle.ts
import { useRef, useCallback } from 'react';

export const useThrottle = <T extends (...args: any[]) => any>(callback: T, delay: number): T => {
  const lastRan = useRef(Date.now());

  return useCallback(
    ((...args) => {
      const now = Date.now();

      if (now - lastRan.current >= delay) {
        callback(...args);
        lastRan.current = now;
      }
    }) as T,
    [callback, delay],
  );
};
```

#### Usage Examples

**Scroll Event**

```tsx
const InfiniteList = () => {
  const loadMore = () => {
    console.log('Loading more...');
  };

  const throttledLoadMore = useThrottle(loadMore, 1000);

  return (
    <FlatList
      data={items}
      onEndReached={throttledLoadMore} // Max once per second
      onEndReachedThreshold={0.5}
    />
  );
};
```

**Button Press**

```tsx
const SubmitButton = () => {
  const handleSubmit = () => {
    submitForm();
  };

  const throttledSubmit = useThrottle(handleSubmit, 2000);

  return (
    <Button
      title="Submit"
      onPress={throttledSubmit} // Max once per 2 seconds
    />
  );
};
```

**Analytics Events**

```tsx
const AnalyticsTracker = () => {
  const trackEvent = (event: string) => {
    analytics.logEvent(event);
  };

  const throttledTrack = useThrottle(trackEvent, 5000);

  return <Button onPress={() => throttledTrack('button_click')} />;
};
```

#### Common Use Cases

- Scroll events
- Button press protection
- Analytics tracking
- Window resize
- Mouse/touch move events
- API rate limiting

---

### usePrevious Hook

Access the previous value from the last render.

```tsx
// src/hooks/usePrevious.ts
import { useRef, useEffect } from 'react';

export const usePrevious = <T,>(value: T): T | undefined => {
  const ref = useRef<T>();

  useEffect(() => {
    ref.current = value;
  }, [value]);

  return ref.current;
};
```

#### Usage Examples

**Compare Values**

```tsx
import { usePrevious } from '@/hooks';

const CounterScreen = () => {
  const [count, setCount] = useState(0);
  const prevCount = usePrevious(count);

  return (
    <View>
      <Text>Current: {count}</Text>
      <Text>Previous: {prevCount ?? 'N/A'}</Text>
      <Button onPress={() => setCount(c => c + 1)} />
    </View>
  );
};
```

**Conditional Effects**

```tsx
const UserProfile = ({ userId }: { userId: string }) => {
  const prevUserId = usePrevious(userId);

  useEffect(() => {
    if (prevUserId && prevUserId !== userId) {
      console.log(`User changed from ${prevUserId} to ${userId}`);
      fetchUserData(userId);
    }
  }, [userId, prevUserId]);

  return <ProfileView userId={userId} />;
};
```

**Animation Triggers**

```tsx
const AnimatedCounter = () => {
  const [value, setValue] = useState(0);
  const prevValue = usePrevious(value);

  const shouldAnimate = prevValue !== undefined && prevValue !== value;

  return (
    <Animated.View style={shouldAnimate ? animationStyle : {}}>
      <Text>{value}</Text>
    </Animated.View>
  );
};
```

#### Common Use Cases

- Value comparison
- Conditional effects
- Animation triggers
- Debug logging
- Undo functionality
- Delta calculations

---

### useToggle Hook

Simplified boolean state management.

```tsx
// src/hooks/useToggle.ts
import { useState, useCallback } from 'react';

export const useToggle = (initialValue = false): [boolean, () => void, (value: boolean) => void] => {
  const [value, setValue] = useState(initialValue);

  const toggle = useCallback(() => {
    setValue(v => !v);
  }, []);

  return [value, toggle, setValue];
};
```

#### Usage Examples

**Modal Toggle**

```tsx
import { useToggle } from '@/hooks';

const MyScreen = () => {
  const [isModalOpen, toggleModal, setModalOpen] = useToggle();

  return (
    <>
      <Button title="Open" onPress={toggleModal} />
      <Modal visible={isModalOpen} onClose={toggleModal}>
        <Text>Modal Content</Text>
      </Modal>
    </>
  );
};
```

**Visibility Toggle**

```tsx
const PasswordInput = () => {
  const [isVisible, toggleVisibility] = useToggle(false);

  return (
    <Input
      secureTextEntry={!isVisible}
      rightIcon={<TouchableOpacity onPress={toggleVisibility}>{isVisible ? <Eye /> : <EyeOff />}</TouchableOpacity>}
    />
  );
};
```

**Accordion**

```tsx
const AccordionItem = ({ title, children }) => {
  const [isExpanded, toggleExpanded] = useToggle(false);

  return (
    <View>
      <TouchableOpacity onPress={toggleExpanded}>
        <Text>{title}</Text>
        {isExpanded ? <ChevronUp /> : <ChevronDown />}
      </TouchableOpacity>
      {isExpanded && <View>{children}</View>}
    </View>
  );
};
```

#### Common Use Cases

- Modal visibility
- Password visibility
- Accordion/collapsible
- Drawer open/close
- Feature flags
- Any boolean state

---

## Platform Hooks

### useColorScheme Hook

Detect system color scheme with web variant support.

```tsx
// src/hooks/useColorScheme.ts
import { useColorScheme as useRNColorScheme } from 'react-native';

export function useColorScheme() {
  return useRNColorScheme() ?? 'light';
}
```

```tsx
// src/hooks/useColorScheme.web.ts
import { useEffect, useState } from 'react';

export function useColorScheme() {
  const [colorScheme, setColorScheme] = useState<'light' | 'dark'>('light');

  useEffect(() => {
    const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
    setColorScheme(mediaQuery.matches ? 'dark' : 'light');

    const listener = (e: MediaQueryListEvent) => {
      setColorScheme(e.matches ? 'dark' : 'light');
    };

    mediaQuery.addEventListener('change', listener);
    return () => mediaQuery.removeEventListener('change', listener);
  }, []);

  return colorScheme;
}
```

#### Usage Example

**Theme Provider**

```tsx
import { useColorScheme } from '@/hooks/useColorScheme';
import { ThemeProvider, DarkTheme, DefaultTheme } from '@react-navigation/native';

export default function RootLayout() {
  const colorScheme = useColorScheme();

  return (
    <ThemeProvider value={colorScheme === 'dark' ? DarkTheme : DefaultTheme}>
      <Stack />
    </ThemeProvider>
  );
}
```

#### Notes

- Automatically uses `.web.ts` variant on web
- Falls back to 'light' if undefined
- Respects system preference
- Updates on system change

---

### useKeyboard Hook

Detect keyboard visibility and height.

```tsx
// src/hooks/useKeyboard.ts
import { useEffect, useState } from 'react';
import { Keyboard, KeyboardEvent } from 'react-native';

export const useKeyboard = () => {
  const [isKeyboardVisible, setKeyboardVisible] = useState(false);
  const [keyboardHeight, setKeyboardHeight] = useState(0);

  useEffect(() => {
    const showListener = Keyboard.addListener('keyboardDidShow', (e: KeyboardEvent) => {
      setKeyboardVisible(true);
      setKeyboardHeight(e.endCoordinates.height);
    });

    const hideListener = Keyboard.addListener('keyboardDidHide', () => {
      setKeyboardVisible(false);
      setKeyboardHeight(0);
    });

    return () => {
      showListener.remove();
      hideListener.remove();
    };
  }, []);

  return { isKeyboardVisible, keyboardHeight };
};
```

#### Usage Examples

**Adjust Layout**

```tsx
import { useKeyboard } from '@/hooks';

const ChatScreen = () => {
  const { keyboardHeight } = useKeyboard();

  return (
    <View style={{ marginBottom: keyboardHeight }}>
      <MessageList />
      <MessageInput />
    </View>
  );
};
```

**Conditional Rendering**

```tsx
const FormScreen = () => {
  const { isKeyboardVisible } = useKeyboard();

  return (
    <View>
      <Form />
      {!isKeyboardVisible && <FloatingButton />}
    </View>
  );
};
```

**Dismiss Keyboard**

```tsx
const SearchScreen = () => {
  const { isKeyboardVisible } = useKeyboard();

  return (
    <TouchableWithoutFeedback onPress={() => isKeyboardVisible && Keyboard.dismiss()}>
      <View>
        <SearchInput />
      </View>
    </TouchableWithoutFeedback>
  );
};
```

#### Common Use Cases

- Adjust layout for keyboard
- Hide elements when keyboard shows
- Dismiss keyboard on tap
- Animate UI elements
- Calculate available space

---

## Hook Exports

### Barrel Exports

```tsx
// src/hooks/index.ts

// Timer hooks
export { useTimeout } from './useTimeout';
export { useInterval } from './useInterval';

// App integration
export { useRateApp } from './useRateApp';
export { useAppState } from './useAppState';

// Value hooks
export { useDebounce } from './useDebounce';
export { useThrottle } from './useThrottle';
export { usePrevious } from './usePrevious';
export { useToggle } from './useToggle';

// Platform hooks
export { useColorScheme } from './useColorScheme';
export { useKeyboard } from './useKeyboard';

// Theme hooks (if using)
export { useThemeColor } from './useThemeColor';
```

---

## Testing Hooks

### Test Pattern with renderHook

```tsx
// src/hooks/__tests__/useDebounce.test.ts
import { renderHook, waitFor } from '@testing-library/react-native';
import { useDebounce } from '../useDebounce';

describe('useDebounce', () => {
  it('should debounce value', async () => {
    const { result, rerender } = renderHook(({ value, delay }) => useDebounce(value, delay), {
      initialProps: { value: 'initial', delay: 500 },
    });

    expect(result.current).toBe('initial');

    rerender({ value: 'updated', delay: 500 });
    expect(result.current).toBe('initial'); // Still old value

    await waitFor(() => expect(result.current).toBe('updated'), {
      timeout: 600,
    });
  });
});
```

### Test Pattern with useEffect

```tsx
// src/hooks/__tests__/useTimeout.test.ts
import { renderHook } from '@testing-library/react-native';
import { useTimeout } from '../useTimeout';

describe('useTimeout', () => {
  jest.useFakeTimers();

  it('should call callback after delay', () => {
    const callback = jest.fn();

    renderHook(() => useTimeout(callback, 1000, true));

    expect(callback).not.toHaveBeenCalled();

    jest.advanceTimersByTime(1000);

    expect(callback).toHaveBeenCalledTimes(1);
  });

  it('should not call callback when start is false', () => {
    const callback = jest.fn();

    renderHook(() => useTimeout(callback, 1000, false));

    jest.advanceTimersByTime(1000);

    expect(callback).not.toHaveBeenCalled();
  });
});
```

---

## Best Practices

### Dependency Arrays

Always include all dependencies in useEffect/useCallback:

```tsx
// ❌ Bad - missing dependency
useEffect(() => {
  fetchData(userId);
}, []);

// ✅ Good - all dependencies listed
useEffect(() => {
  fetchData(userId);
}, [userId]);
```

### useCallback for Callbacks

Wrap callbacks in useCallback to prevent re-renders:

```tsx
// ❌ Bad - creates new function on every render
useTimeout(() => doSomething(), 1000, true);

// ✅ Good - memoized callback
const memoizedCallback = useCallback(() => doSomething(), []);
useTimeout(memoizedCallback, 1000, true);
```

### Cleanup Functions

Always cleanup subscriptions and timers:

```tsx
useEffect(() => {
  const subscription = listener.subscribe();

  return () => {
    subscription.unsubscribe(); // Cleanup
  };
}, []);
```

### Refs for Latest Values

Use refs when you need the latest value without re-running effects:

```tsx
const useInterval = (callback, delay) => {
  const savedCallback = useRef(callback);

  // Update ref on each render
  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);

  // Use ref in effect (no callback dependency)
  useEffect(() => {
    const id = setInterval(() => {
      savedCallback.current();
    }, delay);
    return () => clearInterval(id);
  }, [delay]);
};
```

---

## Checklist

- [ ] Hook file in `src/hooks/useXxx.ts`
- [ ] TypeScript types for parameters and return value
- [ ] useEffect dependencies properly listed
- [ ] Cleanup functions for subscriptions/timers
- [ ] useCallback/useMemo where appropriate
- [ ] Exported from `src/hooks/index.ts`
- [ ] Tests written (if complex logic)
- [ ] JSDoc comments for public APIs (optional)
- [ ] Platform variants (.web.ts) if needed
- [ ] Error handling where applicable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sajinthan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
