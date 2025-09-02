# Project Description

This document will detail what the goal of this project is and what its trying to achieve

## Goal
-
-
-

## Control Flow
- **Happy path last**: Handle error conditions first, success case last
- **Avoid else**: Use early returns instead of nested conditions  
- **Separate conditions**: Prefer multiple if statements over compound conditions

## Version Specific Issues
- **Ruby Version Compatibility:** Ensure code is compatible with the target Ruby version.
- **Rails Version Compatibility:** Ensure code is compatible with the target Rails version.

## Comments
- Add `# frozen_string_literal: true` comment at the top of each .rb file
- **Avoid comments** - write expressive code instead
- When needed, use proper formatting:
  ```ruby
  # Single line with space after
  
  # Multi
  # Line
  # Comment
  ```
- Refactor comments into descriptive function names
