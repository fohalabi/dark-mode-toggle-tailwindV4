# Dark Mode Implementation Guide

A comprehensive guide to implementing dark mode with Tailwind CSS across different frameworks and vanilla JavaScript.

## Table of Contents
- [HTML + Vanilla JavaScript](#html--vanilla-javascript)
- [React + JavaScript](#react--javascript)  
- [React + TypeScript](#react--typescript)
- [Key Features](#key-features)
- [Browser Support](#browser-support)

## HTML + Vanilla JavaScript

### Setup

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dark Mode Toggle</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        // Initialize theme before page renders to prevent flash
        (function() {
            const savedTheme = localStorage.getItem('theme');
            const systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
            
            if (savedTheme === 'dark' || (!savedTheme && systemPrefersDark)) {
                document.documentElement.classList.add('dark');
            }
        })();
    </script>
</head>
<body class="bg-white dark:bg-gray-900 text-gray-900 dark:text-white transition-colors">
    <div class="min-h-screen flex items-center justify-center">
        <button 
            id="themeToggle"
            class="px-4 py-2 bg-gray-200 dark:bg-gray-700 rounded-lg hover:bg-gray-300 dark:hover:bg-gray-600 transition-colors"
        >
            <span class="dark:hidden">Switch to Dark</span>
            <span class="hidden dark:inline">Switch to Light</span>
        </button>
    </div>

    <script>
        class ThemeToggle {
            constructor() {
                this.button = document.getElementById('themeToggle');
                this.init();
            }

            init() {
                this.button.addEventListener('click', () => this.toggleTheme());
                this.updateButtonState();
            }

            toggleTheme() {
                const isDark = document.documentElement.classList.contains('dark');
                this.setTheme(isDark ? 'light' : 'dark');
            }

            setTheme(theme) {
                if (theme === 'dark') {
                    document.documentElement.classList.add('dark');
                } else {
                    document.documentElement.classList.remove('dark');
                }
                localStorage.setItem('theme', theme);
                this.updateButtonState();
            }

            updateButtonState() {
                const isDark = document.documentElement.classList.contains('dark');
                this.button.setAttribute('aria-pressed', isDark.toString());
            }
        }

        document.addEventListener('DOMContentLoaded', () => {
            new ThemeToggle();
        });
    </script>
</body>
</html>
```

## React + JavaScript

### Installation

```bash
npm install react react-dom
# For Tailwind CSS setup, follow: https://tailwindcss.com/docs/guides/create-react-app
```

### Hook Implementation

```jsx
// hooks/useTheme.js
import { useState, useEffect } from 'react';

export const useTheme = () => {
  const [theme, setTheme] = useState(() => {
    if (typeof window !== 'undefined') {
      const saved = localStorage.getItem('theme');
      if (saved) return saved;
      return window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
    }
    return 'light';
  });

  useEffect(() => {
    const root = document.documentElement;
    
    if (theme === 'dark') {
      root.classList.add('dark');
    } else {
      root.classList.remove('dark');
    }
    
    localStorage.setItem('theme', theme);
  }, [theme]);

  const toggleTheme = () => {
    setTheme(prev => prev === 'dark' ? 'light' : 'dark');
  };

  return { theme, toggleTheme };
};
```

### Component Implementation

```jsx
// components/ThemeToggle.jsx
import React from 'react';
import { useTheme } from '../hooks/useTheme';

const ThemeToggle = () => {
  const { theme, toggleTheme } = useTheme();

  return (
    <button
      onClick={toggleTheme}
      className="px-4 py-2 bg-gray-200 dark:bg-gray-700 rounded-lg hover:bg-gray-300 dark:hover:bg-gray-600 transition-colors"
      aria-pressed={theme === 'dark'}
    >
      <span className="dark:hidden">Switch to Dark</span>
      <span className="hidden dark:inline">Switch to Light</span>
    </button>
  );
};

export default ThemeToggle;
```

### App Component

```jsx
// App.jsx
import React from 'react';
import ThemeToggle from './components/ThemeToggle';

function App() {
  return (
    <div className="min-h-screen bg-white dark:bg-gray-900 text-gray-900 dark:text-white transition-colors">
      <div className="flex items-center justify-center min-h-screen">
        <div className="text-center">
          <h1 className="text-2xl font-bold mb-4">Dark Mode Demo</h1>
          <ThemeToggle />
        </div>
      </div>
    </div>
  );
}

export default App;
```

## React + TypeScript

### Hook Implementation

```typescript
// hooks/useTheme.ts
import { useState, useEffect } from 'react';

type Theme = 'light' | 'dark';

interface UseThemeReturn {
  theme: Theme;
  toggleTheme: () => void;
}

export const useTheme = (): UseThemeReturn => {
  const [theme, setTheme] = useState<Theme>(() => {
    if (typeof window !== 'undefined') {
      const saved = localStorage.getItem('theme') as Theme | null;
      if (saved) return saved;
      return window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
    }
    return 'light';
  });

  useEffect(() => {
    const root = document.documentElement;
    
    if (theme === 'dark') {
      root.classList.add('dark');
    } else {
      root.classList.remove('dark');
    }
    
    localStorage.setItem('theme', theme);
  }, [theme]);

  const toggleTheme = (): void => {
    setTheme(prev => prev === 'dark' ? 'light' : 'dark');
  };

  return { theme, toggleTheme };
};
```

### Component Implementation

```typescript
// components/ThemeToggle.tsx
import React from 'react';
import { useTheme } from '../hooks/useTheme';

const ThemeToggle: React.FC = () => {
  const { theme, toggleTheme } = useTheme();

  return (
    <button
      onClick={toggleTheme}
      className="px-4 py-2 bg-gray-200 dark:bg-gray-700 rounded-lg hover:bg-gray-300 dark:hover:bg-gray-600 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500"
      aria-pressed={theme === 'dark'}
      type="button"
    >
      <span className="dark:hidden">Switch to Dark</span>
      <span className="hidden dark:inline">Switch to Light</span>
    </button>
  );
};

export default ThemeToggle;
```

### App Component

```typescript
// App.tsx
import React from 'react';
import ThemeToggle from './components/ThemeToggle';

const App: React.FC = () => {
  return (
    <div className="min-h-screen bg-white dark:bg-gray-900 text-gray-900 dark:text-white transition-colors">
      <div className="flex items-center justify-center min-h-screen">
        <div className="text-center">
          <h1 className="text-2xl font-bold mb-4">Dark Mode Demo</h1>
          <ThemeToggle />
        </div>
      </div>
    </div>
  );
};

export default App;
```

### TypeScript Configuration

Add to your `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "es6"],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx"
  },
  "include": ["src"]
}
```

## Key Features

### ✅ System Preference Detection
Automatically detects user's system dark mode preference on first visit.

### ✅ Persistent Storage
Theme preference is saved in localStorage and restored on page refresh.

### ✅ Flash Prevention
Theme is applied before page renders to prevent flash of wrong theme.

### ✅ Accessibility
- Proper ARIA attributes (`aria-pressed`)
- Keyboard navigation support
- Focus indicators

### ✅ Smooth Transitions
CSS transitions provide smooth theme switching animations.

## Browser Support

- **Chrome**: 76+
- **Firefox**: 67+
- **Safari**: 12.1+
- **Edge**: 79+

## Tailwind CSS Classes Used

| Class | Purpose |
|-------|---------|
| `dark:` | Applies styles only in dark mode |
| `bg-white dark:bg-gray-900` | Light/dark background |
| `text-gray-900 dark:text-white` | Light/dark text |
| `transition-colors` | Smooth color transitions |
| `hidden dark:inline` | Show/hide elements based on theme |

## Important Notes

1. **CDN Usage**: Tailwind CSS CDN supports class-based dark mode by default. No additional configuration needed.

2. **SSR Considerations**: For Next.js or other SSR frameworks, ensure theme initialization runs on the client side only.

3. **Performance**: The localStorage check runs synchronously to prevent theme flash, which is necessary for good UX.

4. **Customization**: You can extend the theme system by adding more themes or custom color schemes.