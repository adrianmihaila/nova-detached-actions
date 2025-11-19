# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased] - Nova 5 Compatibility

### Added
- Support for Laravel Nova 5.x
- Compatibility with both Nova 4.x and Nova 5.x

### Changed
- **PHP requirement updated to 8.1+** (required by Nova 5)
- Updated Vue dependencies to Vue 3.3+ for better Nova 5 compatibility
- Updated Heroicons usage to v2 format (Nova 5 uses Heroicons v2)
- Default icon changed from `hero-more-horiz` to `ellipsis-horizontal` (Heroicons v2 naming)
- Icon rendering now uses Nova's `Icon` component for consistency
- Updated `@vue/compiler-sfc` to ^3.3.0
- Updated `vue-loader` to ^17.0.0
- Updated `sass` and `sass-loader` to latest versions
- Updated `resolve-url-loader` to ^5.0.0

### Removed
- Removed `vue-template-compiler` (Vue 2 dependency, not needed)
- Removed `@vue/compat` (compatibility build, not needed)

### Notes
- When using custom icons with the `icon()` method, ensure you use Heroicons v2 naming conventions
- Example: Use `plus` instead of `add`, `ellipsis-horizontal` instead of `dots-horizontal`
- See [Heroicons v2 documentation](https://heroicons.com/) for available icon names

### Upgrade Instructions

1. **Update Composer dependencies:**
   ```bash
   composer update
   ```

2. **Update NPM dependencies:**
   ```bash
   npm install
   ```

3. **Rebuild assets:**
   ```bash
   npm run production
   ```

4. **Update icon names (if using custom icons):**
   - Review any custom icons you've specified in your actions
   - Update them to use Heroicons v2 naming conventions
   - Example: Change `->icon('menu')` to `->icon('bars-3')` if needed

5. **Test your detached actions:**
   - Verify that all detached actions appear correctly in the toolbar
   - Test action execution to ensure functionality works as expected

