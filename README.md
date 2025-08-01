# How to Create a Professional Dark Mode Toggle with Tailwind CSS v4

Dark mode has become an essential feature in modern web applications. Users expect seamless theme switching that respects their preferences and persists across sessions. In this comprehensive guide, we'll build a production-ready dark mode toggle using Tailwind CSS v4, vanilla JavaScript, and best practices for accessibility and performance.

## Table of Contents

1. [Understanding Dark Mode Implementation](#understanding-dark-mode-implementation)
2. [Setting Up Tailwind CSS v4](#setting-up-tailwind-css-v4)
3. [The Foundation: HTML Structure](#the-foundation-html-structure)
4. [Preventing Flash of Unstyled Content](#preventing-flash-of-unstyled-content)
5. [Building the Toggle Button](#building-the-toggle-button)
6. [JavaScript Implementation](#javascript-implementation)
7. [Styling with Tailwind Dark Mode](#styling-with-tailwind-dark-mode)
8. [Adding Smooth Transitions](#adding-smooth-transitions)
9. [Accessibility Considerations](#accessibility-considerations)
10. [Advanced Features](#advanced-features)
11. [Testing and Browser Support](#testing-and-browser-support)
12. [Production Optimization](#production-optimization)

## Understanding Dark Mode Implementation

Dark mode implementation involves three key components:

1. **Theme Detection**: Determining the user's preference (saved or system)
2. **Theme Application**: Adding/removing CSS classes to switch themes
3. **Persistence**: Saving the user's choice for future visits

The most robust approach uses a combination of localStorage for persistence and the `prefers-color-scheme` media query for system preference detection.

### The Theme Priority Hierarchy

```
1. Saved User Preference (localStorage)
2. System Preference (prefers-color-scheme)
3. Default Theme (usually light)
```

## Setting Up Tailwind CSS v4

Tailwind CSS v4 introduces several improvements over previous versions, including better dark mode handling and enhanced CDN support.

### CDN Integration

For rapid prototyping and small projects, use the CDN:

```html
<script src="https://cdn.tailwindcss.com"></script>
```

### Key Advantages of Tailwind v4

- **Improved Performance**: Faster compilation and smaller bundle sizes
- **Enhanced Dark Mode**: Better support for complex dark mode implementations
- **New Utilities**: Additional classes for modern design patterns
- **Better CDN**: More reliable and faster content delivery

## The Foundation: HTML Structure

Start with a proper HTML foundation that supports theme switching:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dark Mode Toggle</title>
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Critical: Theme initialization script -->
    <script>
        (function() {
            const savedTheme = localStorage.getItem('theme');
            const systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
            
            if (savedTheme === 'dark' || (!savedTheme && systemPrefersDark)) {
                document.documentElement.classList.add('dark');
            }
        })();
    </script>
</head>
<body class="bg-white dark:bg-gray-900 text-gray-900 dark:text-white transition-colors duration-300">
    <!-- Coontent Here -->
</body>
</html>
```

### Critical Implementation Details

**1. Immediate Execution Function**: The theme script runs immediately to prevent FOUC
**2. HTML Element Targeting**: We add the `dark` class to `document.documentElement` (the `<html>` tag)
**3. Fallback Logic**: Graceful degradation if localStorage isn't available

## Preventing Flash of Unstyled Content

Flash of Unstyled Content (FOUC) occurs when the page briefly shows the wrong theme before JavaScript executes. This creates a jarring user experience.

### The Solution: Synchronous Theme Detection

```javascript
(function() {
    const savedTheme = localStorage.getItem('theme');
    const systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
    
    if (savedTheme === 'dark' || (!savedTheme && systemPrefersDark)) {
        document.documentElement.classList.add('dark');
    }
})();
```

**Why This Works:**
- Executes before CSS parsing
- Synchronous execution (no async/await)
- Minimal DOM manipulation
- Runs in the `<head>` section

### Testing FOUC Prevention

To verify FOUC prevention:
1. Set your system to dark mode
2. Clear localStorage
3. Refresh the page multiple times
4. Check for any white flash before dark theme appears

## Building the Toggle Button

A well-designed toggle button provides clear visual feedback about the current state and the action that will occur.

### Button Structure

```html
<button 
    id="themeToggle"
    class="group relative flex items-center justify-center px-6 py-3 bg-gray-100 dark:bg-gray-800 hover:bg-gray-200 dark:hover:bg-gray-700 rounded-full border border-gray-300 dark:border-gray-600 shadow-lg hover:shadow-xl transition-all duration-300 ease-in-out focus:outline-none focus:ring-4 focus:ring-blue-500/20"
    aria-label="Toggle dark mode"
    role="switch"
>
    
    <span class="font-medium transition-colors duration-300">
        <span class="dark:hidden">Switch to Dark</span>
        <span class="hidden dark:inline">Switch to Light</span>
    </span>
</button>
```

### Design Principles

**1. Visual Hierarchy**: Icons and text work together to communicate state
**2. Smooth Animations**: Icons rotate and scale for engaging transitions
**3. Contextual Labels**: Text changes based on current theme
**4. Accessible Focus**: Clear focus indicators for keyboard navigation

## JavaScript Implementation

The JavaScript implementation follows object-oriented principles for maintainability and extensibility.

### Class-Based Architecture

**JavaScript Implementation:**
```javascript
class ThemeToggle {
    constructor() {
        this.button = document.getElementById('themeToggle');
        this.init();
    }

    init() {
        // Event listeners
        this.button.addEventListener('click', () => this.toggleTheme());
        this.button.addEventListener('keydown', (e) => {
            if (e.key === 'Enter' || e.key === ' ') {
                e.preventDefault();
                this.toggleTheme();
            }
        });

        // System preference listener
        window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (e) => {
            if (!localStorage.getItem('theme')) {
                this.setTheme(e.matches ? 'dark' : 'light');
            }
        });

        this.updateButtonState();
    }

    toggleTheme() {
        const isDark = document.documentElement.classList.contains('dark');
        const newTheme = isDark ? 'light' : 'dark';
        this.setTheme(newTheme);
    }

    setTheme(theme) {
        const html = document.documentElement;
        
        if (theme === 'dark') {
            html.classList.add('dark');
        } else {
            html.classList.remove('dark');
        }

        localStorage.setItem('theme', theme);
        this.updateButtonState();
    }

    updateButtonState() {
        const isDark = document.documentElement.classList.contains('dark');
        this.button.setAttribute('aria-checked', isDark.toString());
        this.button.title = isDark ? 'Switch to light mode' : 'Switch to dark mode';
    }
}

// Initialize when DOM is ready
document.addEventListener('DOMContentLoaded', () => {
    new ThemeToggle();
});
```

**TypeScript Implementation:**
```typescript
type Theme = 'light' | 'dark';

interface ThemeToggleOptions {
    buttonId?: string;
    storageKey?: string;
    onThemeChange?: (theme: Theme) => void;
}

class ThemeToggle {
    private button: HTMLButtonElement;
    private readonly storageKey: string;
    private readonly onThemeChange?: (theme: Theme) => void;
    private mediaQuery: MediaQueryList;

    constructor(options: ThemeToggleOptions = {}) {
        this.storageKey = options.storageKey || 'theme';
        this.onThemeChange = options.onThemeChange;
        
        const buttonElement = document.getElementById(options.buttonId || 'themeToggle');
        if (!buttonElement || !(buttonElement instanceof HTMLButtonElement)) {
            throw new Error('Theme toggle button not found or invalid element type');
        }
        
        this.button = buttonElement;
        this.mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
        this.init();
    }

    private init(): void {
        // Event listeners with proper typing
        this.button.addEventListener('click', this.handleClick.bind(this));
        this.button.addEventListener('keydown', this.handleKeydown.bind(this));
        
        // System preference listener
        this.mediaQuery.addEventListener('change', this.handleSystemThemeChange.bind(this));
        
        this.updateButtonState();
    }

    private handleClick(): void {
        this.toggleTheme();
    }

    private handleKeydown(event: KeyboardEvent): void {
        if (event.key === 'Enter' || event.key === ' ') {
            event.preventDefault();
            this.toggleTheme();
        }
    }

    private handleSystemThemeChange(event: MediaQueryListEvent): void {
        if (!this.getSavedTheme()) {
            this.setTheme(event.matches ? 'dark' : 'light');
        }
    }

    private getCurrentTheme(): Theme {
        return document.documentElement.classList.contains('dark') ? 'dark' : 'light';
    }

    private getSavedTheme(): Theme | null {
        const saved = localStorage.getItem(this.storageKey);
        return saved === 'dark' || saved === 'light' ? saved : null;
    }

    public toggleTheme(): void {
        const currentTheme = this.getCurrentTheme();
        const newTheme: Theme = currentTheme === 'dark' ? 'light' : 'dark';
        this.setTheme(newTheme);
    }

    public setTheme(theme: Theme): void {
        const html = document.documentElement;
        
        if (theme === 'dark') {
            html.classList.add('dark');
        } else {
            html.classList.remove('dark');
        }

        try {
            localStorage.setItem(this.storageKey, theme);
        } catch (error) {
            console.warn('Could not save theme preference:', error);
        }

        this.updateButtonState();
        this.onThemeChange?.(theme);
    }

    private updateButtonState(): void {
        const isDark = this.getCurrentTheme() === 'dark';
        this.button.setAttribute('aria-checked', isDark.toString());
        this.button.title = isDark ? 'Switch to light mode' : 'Switch to dark mode';
    }

    public destroy(): void {
        this.button.removeEventListener('click', this.handleClick);
        this.button.removeEventListener('keydown', this.handleKeydown);
        this.mediaQuery.removeEventListener('change', this.handleSystemThemeChange);
    }
}

// Initialize when DOM is ready
document.addEventListener('DOMContentLoaded', () => {
    const themeToggle = new ThemeToggle({
        onThemeChange: (theme) => {
            console.log(`Theme changed to: ${theme}`);
        }
    });
});
```
```

### Key Implementation Features

**1. Event Delegation**: Single class manages all theme-related functionality
**2. State Management**: Centralized theme state with consistent updates
**3. Error Handling**: Graceful degradation if elements aren't found
**4. Memory Management**: Proper event listener setup and cleanup

### System Preference Monitoring

```javascript
window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (e) => {
    if (!localStorage.getItem('theme')) {
        this.setTheme(e.matches ? 'dark' : 'light');
    }
});
```

This ensures the app responds to system theme changes, but only when the user hasn't set a manual preference.

## Styling with Tailwind Dark Mode

Tailwind's dark mode implementation uses the `dark:` prefix to apply styles when the `dark` class is present on a parent element.

### Basic Dark Mode Classes

```css
/* Background colors */
bg-white dark:bg-gray-900

/* Text colors */
text-gray-900 dark:text-white

/* Border colors */
border-gray-300 dark:border-gray-600

/* Hover states */
hover:bg-gray-200 dark:hover:bg-gray-700
```

### Advanced Styling Patterns

**1. Gradient Adaptations**:
```css
bg-gradient-to-r from-blue-600 to-purple-600 dark:from-blue-400 dark:to-purple-400
```

**2. Shadow Variations**:
```css
shadow-lg dark:shadow-gray-800/50
```

**3. Complex State Combinations**:
```css
bg-gray-100 dark:bg-gray-800 hover:bg-gray-200 dark:hover:bg-gray-700 focus:ring-blue-500/20
```

### Color Strategy

Effective dark mode requires careful color selection:

**Light Mode Colors:**
- Background: White, light grays (50-100)
- Text: Dark grays (700-900), black
- Accents: Vibrant colors (500-600)

**Dark Mode Colors:**
- Background: Dark grays (800-900), black
- Text: Light grays (100-300), white
- Accents: Lighter variants (400-500)

## Adding Smooth Transitions

Transitions make theme switching feel polished and professional.

### Transition Classes

```css
/* Basic color transitions */
transition-colors duration-300

/* Comprehensive transitions */
transition-all duration-300 ease-in-out

/* Custom timing */
transition-colors duration-500 ease-[cubic-bezier(0.4,0,0.2,1)]
```

### Animation Strategies

**1. Icon Animations**:
```css
/* Rotation + Scale */
transition-all duration-300 ease-in-out dark:scale-0 dark:rotate-90

/* Opacity + Transform */
transition-opacity duration-300 opacity-100 dark:opacity-0
```

**2. Content Transitions**:
```css
/* Conditional visibility */
<span class="dark:hidden">Light Mode Text</span>
<span class="hidden dark:inline">Dark Mode Text</span>
```

**3. Performance Considerations**:
- Use `transform` and `opacity` for best performance
- Avoid animating `width`, `height`, or `left/top`
- Limit simultaneous animations

## Accessibility Considerations

Accessibility is crucial for inclusive design. Our implementation addresses multiple accessibility concerns.

### ARIA Attributes

```html
<button 
    aria-label="Toggle dark mode"
    role="switch"
    aria-checked="false"
    title="Switch to dark mode"
>
```

**Key ARIA Features:**
- `aria-label`: Describes the button's purpose
- `role="switch"`: Indicates toggle functionality
- `aria-checked`: Current state for screen readers
- `title`: Additional context on hover

### Keyboard Navigation

```javascript
this.button.addEventListener('keydown', (e) => {
    if (e.key === 'Enter' || e.key === ' ') {
        e.preventDefault();
        this.toggleTheme();
    }
});
```

**Supported Keys:**
- `Enter`: Activates the toggle
- `Space`: Alternative activation method
- `Tab`: Standard focus navigation

### Focus Management

```css
focus:outline-none focus:ring-4 focus:ring-blue-500/20
```

Clear focus indicators help keyboard users navigate effectively.

### Reduced Motion Support

For users with motion sensitivity:

```css
@media (prefers-reduced-motion: reduce) {
    .transition-all {
        transition-duration: 0.01ms !important;
    }
}
```

## React + TypeScript Integration

### Custom Hook Implementation

```typescript
// hooks/useTheme.ts
import { useState, useEffect, useCallback } from 'react';

type Theme = 'light' | 'dark';

interface UseThemeReturn {
  theme: Theme;
  toggleTheme: () => void;
  setTheme: (theme: Theme) => void;
  systemTheme: Theme;
}

export const useTheme = (storageKey: string = 'theme'): UseThemeReturn => {
  const [theme, setThemeState] = useState<Theme>('light');
  const [systemTheme, setSystemTheme] = useState<Theme>('light');

  // Get system preference
  const getSystemTheme = useCallback((): Theme => {
    return window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
  }, []);

  // Apply theme to DOM
  const applyTheme = useCallback((newTheme: Theme): void => {
    const html = document.documentElement;
    
    if (newTheme === 'dark') {
      html.classList.add('dark');
    } else {
      html.classList.remove('dark');
    }
  }, []);

  // Set theme with persistence
  const setTheme = useCallback((newTheme: Theme): void => {
    setThemeState(newTheme);
    applyTheme(newTheme);
    
    try {
      localStorage.setItem(storageKey, newTheme);
    } catch (error) {
      console.warn('Could not save theme preference:', error);
    }
  }, [storageKey, applyTheme]);

  // Toggle between themes
  const toggleTheme = useCallback((): void => {
    setTheme(theme === 'dark' ? 'light' : 'dark');
  }, [theme, setTheme]);

  useEffect(() => {
    // Initialize theme
    const savedTheme = localStorage.getItem(storageKey) as Theme | null;
    const systemPreference = getSystemTheme();
    
    setSystemTheme(systemPreference);
    
    const initialTheme = savedTheme || systemPreference;
    setThemeState(initialTheme);
    applyTheme(initialTheme);

    // Listen for system preference changes
    const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
    const handleSystemThemeChange = (e: MediaQueryListEvent): void => {
      const newSystemTheme = e.matches ? 'dark' : 'light';
      setSystemTheme(newSystemTheme);
      
      // Only update if no manual preference is set
      if (!localStorage.getItem(storageKey)) {
        setThemeState(newSystemTheme);
        applyTheme(newSystemTheme);
      }
    };

    mediaQuery.addEventListener('change', handleSystemThemeChange);
    
    return () => {
      mediaQuery.removeEventListener('change', handleSystemThemeChange);
    };
  }, [storageKey, getSystemTheme, applyTheme]);

  return { theme, toggleTheme, setTheme, systemTheme };
};
```

### Context Provider Implementation

```typescript
// contexts/ThemeContext.tsx
import React, { createContext, useContext, ReactNode } from 'react';
import { useTheme } from '../hooks/useTheme';

type Theme = 'light' | 'dark';

interface ThemeContextType {
  theme: Theme;
  toggleTheme: () => void;
  setTheme: (theme: Theme) => void;
  systemTheme: Theme;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export const useThemeContext = (): ThemeContextType => {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useThemeContext must be used within a ThemeProvider');
  }
  return context;
};

interface ThemeProviderProps {
  children: ReactNode;
  storageKey?: string;
}

export const ThemeProvider: React.FC<ThemeProviderProps> = ({ 
  children, 
  storageKey = 'theme' 
}) => {
  const themeUtils = useTheme(storageKey);

  return (
    <ThemeContext.Provider value={themeUtils}>
      {children}
    </ThemeContext.Provider>
  );
};
```

### Toggle Component Implementation

```typescript
// components/ThemeToggle.tsx
import React from 'react';
import { useThemeContext } from '../contexts/ThemeContext';

interface ThemeToggleProps {
  className?: string;
  showLabel?: boolean;
}

const ThemeToggle: React.FC<ThemeToggleProps> = ({ 
  className = '', 
  showLabel = true 
}) => {
  const { theme, toggleTheme } = useThemeContext();

  const handleKeyDown = (event: React.KeyboardEvent<HTMLButtonElement>): void => {
    if (event.key === 'Enter' || event.key === ' ') {
      event.preventDefault();
      toggleTheme();
    }
  };

  return (
    <button
      onClick={toggleTheme}
      onKeyDown={handleKeyDown}
      className={`group relative flex items-center justify-center px-6 py-3 bg-gray-100 dark:bg-gray-800 hover:bg-gray-200 dark:hover:bg-gray-700 rounded-full border border-gray-300 dark:border-gray-600 shadow-lg hover:shadow-xl transition-all duration-300 ease-in-out focus:outline-none focus:ring-4 focus:ring-blue-500/20 ${className}`}
      aria-label="Toggle dark mode"
      aria-checked={theme === 'dark'}
      role="switch"
      title={theme === 'dark' ? 'Switch to light mode' : 'Switch to dark mode'}
    >
      {/* Sun Icon */}
      <svg 
        className={`w-5 h-5 text-yellow-500 transition-all duration-300 ease-in-out ${
          theme === 'dark' ? 'scale-0 rotate-90' : 'scale-100 rotate-0'
        }`}
        fill="currentColor" 
        viewBox="0 0 20 20"
        aria-hidden="true"
      >
        <path fillRule="evenodd" d="M10 2a1 1 0 011 1v1a1 1 0 11-2 0V3a1 1 0 011-1zm4 8a4 4 0 11-8 0 4 4 0 018 0zm-.464 4.95l.707.707a1 1 0 001.414-1.414l-.707-.707a1 1 0 00-1.414 1.414zm2.12-10.607a1 1 0 010 1.414l-.706.707a1 1 0 11-1.414-1.414l.707-.707a1 1 0 011.414 0zM17 11a1 1 0 100-2h-1a1 1 0 100 2h1zm-7 4a1 1 0 011 1v1a1 1 0 11-2 0v-1a1 1 0 011-1zM5.05 6.464A1 1 0 106.465 5.05l-.708-.707a1 1 0 00-1.414 1.414l.707.707zm1.414 8.486l-.707.707a1 1 0 01-1.414-1.414l.707-.707a1 1 0 011.414 1.414zM4 11a1 1 0 100-2H3a1 1 0 000 2h1z" clipRule="evenodd" />
      </svg>
      
      {/* Moon Icon */}
      <svg 
        className={`w-5 h-5 text-blue-400 absolute transition-all duration-300 ease-in-out ${
          theme === 'dark' ? 'scale-100 rotate-0' : 'scale-0 rotate-90'
        }`}
        fill="currentColor" 
        viewBox="0 0 20 20"
        aria-hidden="true"
      >
        <path d="M17.293 13.293A8 8 0 016.707 2.707a8.001 8.001 0 1010.586 10.586z" />
      </svg>
      
      {showLabel && (
        <span className="ml-3 font-medium transition-colors duration-300">
          {theme === 'dark' ? 'Switch to Light' : 'Switch to Dark'}
        </span>
      )}
    </button>
  );
};

export default ThemeToggle;
```

### Usage in React App

```typescript
// App.tsx
import React from 'react';
import { ThemeProvider } from './contexts/ThemeContext';
import ThemeToggle from './components/ThemeToggle';

const App: React.FC = () => {
  return (
    <ThemeProvider>
      <div className="min-h-screen bg-white dark:bg-gray-900 text-gray-900 dark:text-white transition-colors duration-300">
        <header className="p-4 border-b border-gray-200 dark:border-gray-700">
          <div className="flex justify-between items-center">
            <h1 className="text-2xl font-bold">My App</h1>
            <ThemeToggle />
          </div>
        </header>
        <main className="p-4">
          {/* Your app content */}
        </main>
      </div>
    </ThemeProvider>
  );
};

export default App;
```

### System Preference Detection

The `prefers-color-scheme` media query detects system-level theme preferences:

```javascript
const systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
```

**Browser Support:**
- Chrome 76+
- Firefox 67+
- Safari 12.1+
- Edge 79+

### Local Storage Management

```javascript
// Save preference
localStorage.setItem('theme', 'dark');

// Retrieve preference
const savedTheme = localStorage.getItem('theme');

// Remove preference (fall back to system)
localStorage.removeItem('theme');
```

**Error Handling:**
```javascript
try {
    localStorage.setItem('theme', theme);
} catch (error) {
    // Handle storage quota exceeded or privacy settings
    console.warn('Could not save theme preference:', error);
}
```

### Multiple Toggle Instances

For apps with multiple toggle buttons:

```javascript
class ThemeManager {
    constructor() {
        this.toggles = [];
        this.init();
    }

    init() {
        document.querySelectorAll('[data-theme-toggle]').forEach(button => {
            this.toggles.push(new ThemeToggle(button));
        });
    }

    syncToggles() {
        this.toggles.forEach(toggle => toggle.updateButtonState());
    }
}
```

## Testing and Browser Support

### Cross-Browser Testing

**Modern Browsers (Full Support):**
- Chrome 76+: Full support including prefers-color-scheme
- Firefox 67+: Complete implementation
- Safari 12.1+: All features supported
- Edge 79+: Chromium-based, full support

**Legacy Browsers (Graceful Degradation):**
- Internet Explorer: Basic toggle works, no transitions
- Older mobile browsers: Core functionality preserved

### Testing Checklist

1. **Theme Persistence**: Refresh page, check theme maintains
2. **System Preference**: Change OS theme, verify app responds
3. **FOUC Prevention**: No white flash on page load
4. **Keyboard Navigation**: Tab to button, activate with Enter/Space
5. **Screen Reader**: Test with VoiceOver/NVDA
6. **Mobile**: Touch interaction works correctly
7. **Performance**: No layout thrashing during transitions

### Automated Testing

```javascript
// Jest test example
describe('ThemeToggle', () => {
    test('should toggle theme class on button click', () => {
        const toggle = new ThemeToggle();
        const initialState = document.documentElement.classList.contains('dark');
        
        toggle.toggleTheme();
        
        expect(document.documentElement.classList.contains('dark')).toBe(!initialState);
    });
});
```

## Production Optimization

### Performance Considerations

**1. Minimize DOM Queries:**
```javascript
// Good: Cache references
constructor() {
    this.button = document.getElementById('themeToggle');
    this.html = document.documentElement;
}

// Avoid: Repeated queries
toggleTheme() {
    document.documentElement.classList.toggle('dark'); // Repeated query
}
```

**2. Debounce Rapid Interactions:**
```javascript
toggleTheme = debounce(() => {
    // Theme toggle logic
}, 100);
```

**3. CSS Optimization:**
```css
/* Use transform instead of changing layout properties */
transform: scale(0) rotate(90deg);

/* Prefer opacity for show/hide */
opacity: 0;
```

### Bundle Size Optimization

**Tailwind Purging:**
```javascript
// tailwind.config.js
module.exports = {
    content: ['./src/**/*.{html,js}'],
    darkMode: 'class',
    // ... other config
}
```

**Critical CSS:**
```html
<style>
    /* Inline critical theme styles */
    .dark { color-scheme: dark; }
    body { transition: background-color 0.3s; }
</style>
```

### Security Considerations

**LocalStorage Safety:**
```javascript
// Validate stored values
const isValidTheme = (theme) => ['light', 'dark'].includes(theme);

const savedTheme = localStorage.getItem('theme');
if (savedTheme && isValidTheme(savedTheme)) {
    this.setTheme(savedTheme);
}
```

**Content Security Policy:**
```html
<meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline' cdn.tailwindcss.com;">
```

## Conclusion

Building a professional dark mode toggle requires attention to multiple technical and UX considerations. Key takeaways:

1. **Prevent FOUC** with synchronous theme detection
2. **Respect user preferences** through system detection and persistence
3. **Prioritize accessibility** with proper ARIA attributes and keyboard support
4. **Optimize performance** with efficient DOM manipulation and transitions
5. **Test thoroughly** across browsers and assistive technologies

The implementation we've built provides a solid foundation that can be adapted for any project size, from simple websites to complex web applications. The modular JavaScript architecture makes it easy to extend with additional features like theme customization, multiple theme options, or integration with design systems.

By following these patterns and best practices, you'll create dark mode functionality that delights users and maintains professional standards for accessibility and performance.