# Project Context

This repository is the frontend for a SaaS Delivery Order Management System for delivery companies. The project is in the planning and requirements-analysis phase; business requirements and UI/UX flows have not yet been finalized.

## Current Baseline

- React with JavaScript and JSX
- Vite
- The repository currently contains the default React/Vite starter UI.
- The package scripts include `dev`, `build`, `lint` (Oxlint), and `preview`.

Routing, state management, styling, API client, and testing tools are not yet selected. Treat examples such as React Router, Zustand, React Query, Redux Toolkit, Tailwind CSS, Axios, Fetch API, Vitest, and React Testing Library as candidates, not decisions.

## Requirements-First Work

- Do not implement application features, invent business rules, or treat starter-template content as a product requirement until the stakeholder has established the relevant requirements.
- During discovery, ask focused questions about user roles, workflows, screens, data, and edge cases instead of assuming them.
- Clearly distinguish confirmed requirements from assumptions and open questions.
- Do not make consequential architecture or dependency choices before the requirements that justify them are understood. Present proposed changes and their tradeoffs before making such decisions.

## Engineering Principles

- Use component-driven development: build reusable, modular UI components at an appropriate level of reuse.
- Separate UI from business logic, hooks, and API/service code.
- Use custom hooks to encapsulate complex UI logic or state when that improves clarity.
- Keep backend communication in a centralized API/service layer.
- Choose a clean, maintainable folder structure—feature-based or layer-based as best fits the confirmed scope.
- Prefer maintainability and scalability without premature abstraction or unnecessary dependencies.
- Preserve the JavaScript/JSX baseline unless a change in language is explicitly agreed.
- Follow existing project conventions and keep changes focused on the confirmed request.

## Validation

- Use the existing project scripts where relevant: `npm run lint` and `npm run build`.
- Add or change validation tooling only after requirements and the testing approach have been agreed.
