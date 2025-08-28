# Mario Kart Wii - Random Track Picker Guidelines

## Project Overview

This application is a simple, interactive tool that randomly selects tracks from Mario Kart Wii. It displays a grid of all 32 tracks from the game and highlights a random selection when the user clicks the "Pick Random Track" button.

**Key Features:**
- Visual display of all Mario Kart Wii tracks
- Random track selection with visual highlighting
- Responsive design that works across different screen sizes

## Development Environment Setup

### Prerequisites
- Node.js (version ^20.19.0 or >=22.12.0)
- npm (comes with Node.js)

### Installation
1. Clone the repository
2. Install dependencies:
   ```sh
   npm install
   ```

### Development Server
Run the development server with hot-reload:
```sh
npm run dev
```

### Building for Production
Create a production build:
```sh
npm run build
```

Preview the production build:
```sh
npm run preview
```

## Project Structure

```
mkab_picker/
├── public/
│   ├── mkw_map_names.jpg                    # Track map image
│   ├── Mario_Kart_World_-_Track_Map.webp    # Additional track map
│   ├── MarioKartWorld_World_Map_Inner.webp  # World map inner image
│   ├── MarioKartWorld_World_Map_Stages.webp # World map stages image
│   └── track-picker.html                    # Standalone track picker page
├── src/
│   ├── assets/                              # Static assets like images and global styles
│   │   └── logo.svg                         # Vue logo
│   ├── components/                          # Vue components
│   │   ├── HelloWorld.vue                   # Welcome component
│   │   ├── RacePicker.vue                   # Main track picker functionality
│   │   ├── TheWelcome.vue                   # Welcome section component
│   │   ├── WelcomeItem.vue                  # Individual welcome item component
│   │   └── icons/                           # SVG icons for the application
│   └── App.vue                              # Root component
│   └── main.js                              # Application entry point
├── index.html                               # Entry HTML file
├── vite.config.js                           # Vite configuration
└── package.json                             # Project dependencies and scripts
```

## Component Documentation

### RacePicker.vue
The core component of the application that handles the random track selection.

**Functionality:**
- Displays a grid image of all 32 Mario Kart Wii tracks
- Calculates grid cell dimensions based on the loaded image
- Randomly selects a track when the user clicks the button
- Highlights the selected track with an animated overlay

**Key Methods:**
- `handleImageLoad()`: Sets up the grid dimensions when the track image loads
- `pickRandomRace()`: Selects a random track and animates the highlight

## Styling Guidelines

- The application uses scoped CSS in each component
- Primary color scheme:
  - Red (#e60012) for buttons and interactive elements
  - Yellow highlight (rgba(255, 255, 0, 0.3)) for selected tracks
- Responsive design principles should be followed for all components

## Best Practices

### Vue Component Structure
- Use the Composition API with `<script setup>` syntax
- Keep components focused on a single responsibility
- Use props for passing data down to child components
- Use emits for communicating up to parent components

### Code Style
- Use meaningful variable and function names
- Add comments for complex logic
- Follow consistent indentation (2 spaces)
- Use camelCase for JavaScript variables and functions
- Use kebab-case for custom component names in templates

### Performance Considerations
- Optimize images for web use
- Avoid unnecessary re-renders
- Use lazy loading for components when appropriate

## Contribution Guidelines

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Pull Request Process
- Ensure your code follows the project's style guidelines
- Update documentation as needed
- Include screenshots for UI changes
- Reference any relevant issues

## Deployment

### Building for Production
```sh
npm run build
```

The built files will be in the `dist` directory, which can be deployed to any static hosting service.

### Recommended Hosting Options
- GitHub Pages
- Netlify
- Vercel
- Any static file hosting service

## Resources

- [Vue.js Documentation](https://vuejs.org/)
- [Vite Documentation](https://vitejs.dev/)
- [Mario Kart Wii Wiki](https://www.mariowiki.com/Mario_Kart_Wii) (for track information)

---

*Last updated: August 28, 2025*