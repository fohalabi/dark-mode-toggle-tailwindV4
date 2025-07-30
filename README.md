# dark-mode-toggle-tailwindV4
> “How to Build a Dark Mode Toggle in Tailwind CSS (v4)”

🌙 How to Build a Dark Mode Toggle in Tailwind CSS (v4)

> A clean, responsive dark mode switch using native class toggling and Tailwind CSS v4 — no tailwind.config.js needed.


🧭 Overview

Tailwind CSS v4 changed the game by removing the config file by default and enabling out-of-the-box dark mode support via the class strategy.

In this tutorial, you'll learn how to:

Create a toggle button to switch themes

Use Tailwind’s native dark mode support (dark:)

Persist the selected theme with localStorage

Make the UI instantly switch between light/dark


This works for any HTML/JS or React-based frontend — and is perfect for landing pages, portfolios, or blogs.


---

🛠 Tools Used

Tailwind CSS v4 (CDN version or PostCSS)

Vanilla JavaScript or React

No Tailwind config required!



---

🧱 HTML Structure (CDN Version Example)

Here’s the base layout:

<!DOCTYPE html>
<html lang="en" class="dark">
  <head>
    <meta charset="UTF-8" />
    <title>Dark Mode Toggle</title>
    <script src="https://cdn.tailwindcss.com"></script>
  </head>
  <body class="bg-white text-black dark:bg-gray-900 dark:text-white transition-colors duration-300">

    <div class="min-h-screen flex items-center justify-center">
      <button id="toggle-theme" class="px-4 py-2 bg-gray-800 text-white dark:bg-white dark:text-black rounded">
        Toggle Theme
      </button>
    </div>

    <script src="toggle.js"></script>
  </body>
</html>


---

🧠 Core Idea: Add/Remove dark Class on <html>

You don’t need Tailwind config to enable dark mode.
Just toggle the dark class manually using JavaScript.


---

🧩 JavaScript Logic (toggle.js)

// Get current theme from localStorage
const currentTheme = localStorage.getItem('theme');

// Apply on load
if (currentTheme === 'dark') {
  document.documentElement.classList.add('dark');
} else {
  document.documentElement.classList.remove('dark');
}

// Toggle button logic
document.getElementById('toggle-theme').addEventListener('click', () => {
  const isDark = document.documentElement.classList.contains('dark');
  
  if (isDark) {
    document.documentElement.classList.remove('dark');
    localStorage.setItem('theme', 'light');
  } else {
    document.documentElement.classList.add('dark');
    localStorage.setItem('theme', 'dark');
  }
});


---

💡 Optional: Respect User’s System Theme (Bonus)

If no saved preference, default to system:

const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;

if (!localStorage.getItem('theme')) {
  if (prefersDark) {
    document.documentElement.classList.add('dark');
  } else {
    document.documentElement.classList.remove('dark');
  }
}


---

✅ Result

When you click the “Toggle Theme” button:

The dark class is added/removed from <html>

Tailwind instantly switches all dark: variants

The choice is saved to localStorage

On reload, your preference is respected



---

⚠️ Common Gotchas

Issue	Fix

Page flickers between themes on load	Add a script before page render to apply dark class early
Doesn’t persist	Check localStorage keys & make sure toggle script runs
dark: styles not working	Ensure Tailwind is loaded from CDN or built correctly



---

🧩 CodeSandbox Demo

> 🔗 Live Demo
💾 GitHub Repo



(Replace with your actual links when ready)


---

🚀 What’s Next?

Add a sun/moon icon toggle using Lucide or Heroicons

Animate the transition with transition-colors

Wrap logic into a React Hook (useDarkMode)

Combine with Framer Motion for smooth button animation



---

📎 Summary

You just built a zero-config, fast-loading dark mode feature using Tailwind v4.

✅ No config file
✅ Full control over theming
✅ Works in plain HTML, React, Vite, and more


---

💬 Want more?

If you're writing documentation like this or want to contribute to projects using Tailwind, React, or Vite — this pattern is exactly what projects are looking for in contributors.


---

Would you like this in:

A GitHub-ready README version?

A Medium version (polished prose)?

Or a React version with hooks and component structure?


Say the word, Lyra continues.

