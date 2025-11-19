# Laravel Nova 5 Compatibility Guide

This document outlines the changes made to make the package compatible with Laravel Nova 5 while maintaining backward compatibility with Nova 4.

## Summary of Changes

### 1. PHP Requirements (composer.json)
- **Updated PHP version requirement from `>=7.1.0` to `^8.1`**
- **Updated Nova constraint to `^4.0|^5.0`** to support both versions

### 2. Frontend Dependencies (package.json)
- Updated Vue to `^3.3.0` (from `^3.0`)
- Updated `@vue/compiler-sfc` to `^3.3.0` (from `^3.2.22`)
- Updated `vue-loader` to `^17.0.0` (from `^16.8.3`)
- Updated `sass` to `^1.69.0` (from `^1.51.0`)
- Updated `sass-loader` to `^13.3.0` (from `^12.6.0`)
- Updated `resolve-url-loader` to `^5.0.0` (from `^3.1.1`)
- **Removed `vue-template-compiler`** (Vue 2 only, not needed)
- **Removed `@vue/compat`** (compatibility build, not needed)

### 3. Icon Updates (Heroicons v2 Compatibility)

Nova 5 uses Heroicons v2, which has different naming conventions:

**Changed Files:**
- `resources/js/components/ActionButton.vue` - Updated icon rendering to use Nova's `Icon` component
- `resources/js/components/InvisibleActionsDropdown.vue` - Changed default icon from `hero-more-horiz` to `ellipsis-horizontal`
- `resources/js/mixins/DetachedAction.js` - Changed default icon from `hero-more-horiz` to `ellipsis-horizontal`

**Icon Name Changes (Examples):**
- `hero-more-horiz` → `ellipsis-horizontal`
- `add` → `plus`
- `upload` → `arrow-up-tray`
- `download` → `arrow-down-tray`
- `dots-horizontal` → `ellipsis-horizontal`
- `dots-vertical` → `ellipsis-vertical`

See [Heroicons v2 documentation](https://heroicons.com/) for the complete list.

### 4. Documentation Updates
- Updated README.md to indicate Nova 4 and Nova 5 compatibility
- Added requirements section
- Added notes about Heroicons v2 naming conventions
- Created CHANGELOG.md

## What You Need to Do

### For Package Users

1. **Update your environment to meet requirements:**
   - Ensure you have PHP 8.1 or higher
   - Ensure you have Laravel Nova 4.x or 5.x installed

2. **Install the updated package:**
   ```bash
   composer update gobrightspot/nova-detached-actions
   ```

3. **Rebuild your assets:**
   ```bash
   npm run production
   # or
   npm run dev
   ```

4. **Update custom icon names (if you use the `icon()` method):**
   
   If you have detached actions that specify custom icons, update them to use Heroicons v2 naming:
   
   ```php
   // Before (Nova 4 / Heroicons v1)
   (new ExportUsers)->icon('download')
   
   // After (Nova 5 / Heroicons v2)
   (new ExportUsers)->icon('arrow-down-tray')
   ```

5. **Test your detached actions:**
   - Verify buttons appear in the toolbar
   - Test action execution
   - Check that icons display correctly

### For Package Developers

If you're maintaining this package, the changes have been made to support both Nova 4 and Nova 5. The package should work with:

- Laravel Nova 4.x (PHP 8.1+)
- Laravel Nova 5.x (PHP 8.1+)

## Known Limitations

### Internal Component Dependencies

This package extends Nova's internal components:
- `@/views/Detail`
- `@/components/ResourceTableToolbar`
- `@/mixins/HandlesActions`
- `@/mixins/InteractsWithResourceInformation`

These are internal APIs that may change between Nova versions. If you encounter issues:

1. Ensure you have the latest version of this package
2. Check that your Nova installation is up to date
3. Verify that the `vendor/laravel/nova/resources/js` directory exists and contains the expected structure

### Testing Recommendations

After upgrading, test the following scenarios:

1. **Index View:**
   - [ ] Detached actions appear in the toolbar
   - [ ] Actions execute correctly without resource selection
   - [ ] Custom icons display properly
   - [ ] Invisible actions dropdown works (if you have >3 actions)

2. **Detail View:**
   - [ ] Detached actions appear in the detail toolbar
   - [ ] Actions execute with the current resource context
   - [ ] Icons and styling are correct

3. **Action Modals:**
   - [ ] Confirmation modals open correctly
   - [ ] Form fields display properly
   - [ ] Actions execute and return proper responses
   - [ ] Success/error messages display

## Troubleshooting

### Icons not displaying
- Verify you're using Heroicons v2 names
- Clear your browser cache
- Rebuild assets: `npm run production`

### Actions not appearing
- Check Nova assets are published: `php artisan nova:publish`
- Verify the action has the correct toolbar visibility settings
- Check browser console for JavaScript errors

### Build errors
- Delete `node_modules` and `package-lock.json`
- Run `npm install` again
- Ensure you have Node.js 16+ installed

### PHP Errors
- Verify you're running PHP 8.1 or higher
- Run `composer update` to ensure all dependencies are compatible
- Clear Laravel caches: `php artisan optimize:clear`

## Support

If you encounter issues specific to Nova 5 compatibility, please:

1. Check this guide first
2. Verify your environment meets all requirements
3. Review the CHANGELOG.md for recent changes
4. Open an issue on the GitHub repository with:
   - Your PHP version
   - Your Nova version
   - Complete error messages
   - Steps to reproduce

## Additional Resources

- [Laravel Nova 5 Upgrade Guide](https://nova.laravel.com/docs/v5/upgrade)
- [Heroicons v2 Documentation](https://heroicons.com/)
- [Vue 3 Migration Guide](https://v3-migration.vuejs.org/)

