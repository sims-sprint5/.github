
# Corporate Identity Changes

During the software development process, we requested the ESARDI team to create a corporate identity for us, including the selection of colors, typography, and a logo.

The stylesheet was provided during Sprint 5, and we fully implemented it during Sprint 6.

## Process

### style.css

To successfully apply this new corporate identity, we had to make architectural changes, as colors and styles were previously hardcoded throughout the components.
We centralized the styling by creating a main `style.css` file. In this file, we defined all CSS variables for the two themes we decided to support (Light and Dark), ensuring that colors adapt automatically based on the active theme.

We also established global typography rules, setting the "Switzer" font as the default, along with base styles for standard HTML elements.


### tailwind.config.js

Since we use Tailwind CSS, we updated the `tailwind.config.js` file. This configuration acts as a bridge between the custom CSS variables defined in `style.css` and the Tailwind utility framework.
Within this file, we mapped our custom variables to Tailwind's theme configuration, creating standard classes that utilize our new design system.

### Applying Classes to Elements

Finally, we went through our UI components and replaced the old, hardcoded styles and raw CSS with the newly configured Tailwind classes, standardizing the implementation across the entire project.

## Custom Enhancements

To further enhance the visual experience, we added custom interactive touches:
- **Hover effects**: We added hover states to interactive elements like buttons, incorporating slight scaling interactions and subtle color shifts.
- **Glassmorphism**: We introduced blur effects and transparency to the login and registration pages for a more modern look.
- **Theme Switcher**: As mentioned earlier, we built a toggle switch allowing users to seamlessly transition between Light and Dark modes, significantly improving accessibility and overall user experience.

