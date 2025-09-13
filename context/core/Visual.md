# StoryWriter Visual Design System

**Design Language**: Glass-Morphism with Continuous Curves  
**Theme**: Dark-First with Light Mode Support  
**Architecture**: Unified Component Styling

## Design Philosophy

StoryWriter implements a sophisticated **glass-morphism design system** with consistent materials, shadows, and animations across all platforms.

### Core Principles
1. **Glass-Morphism**: Ultra-thin materials with depth
2. **Continuous Corners**: Soft, modern aesthetic
3. **Adaptive Opacity**: State-based visual feedback
4. **Platform Harmony**: Native feel on each platform

## Color System

### Semantic Colors
```swift
// Text
.primary        // Adapts to color scheme
.secondary      // 60% opacity
.accentColor    // System blue

// Backgrounds
.ultraThinMaterial    // Primary glass effect
.regularMaterial      // Panels and containers
Color.secondary.opacity(0.05)  // Subtle tints
```

### Application Background
```swift
// Dark Mode
LinearGradient(
    colors: [Color(white: 0.05), Color(white: 0.02)],
    startPoint: .topLeading,
    endPoint: .bottomTrailing
)

// Light Mode  
LinearGradient(
    colors: [Color(white: 0.98), Color(white: 0.95)],
    startPoint: .topLeading,
    endPoint: .bottomTrailing
)
```

### Content Type Colors
```swift
// Writing Elements
.chapter: .blue
.scene: .purple
.character: .green
.note: .yellow

// Index Cards
.plot: .orange
.subplot: .pink
.theme: .indigo
.idea: .cyan
```

## Typography

### Font System
```swift
// Design: .rounded for friendly feel
.system(size: X, weight: .semibold, design: .rounded)

// Sizes
Title: 15pt
Body: 14pt
Caption: 13pt
Small: 11pt

// Weights
.semibold  // Headers, emphasis
.regular   // Body text
.medium    // Icons

// Writer-specific
Editor: 11-24pt (user adjustable)
Line Numbers: 11pt monospaced
Status Bar: 12pt regular
```

### Platform Typography
```swift
// macOS
ToolbarStyle.macOS:
  glyphSize: 16pt
  buttonSize: 36x32pt
  weight: .regular

// iOS
ToolbarStyle.iOS:
  glyphSize: 18pt
  buttonSize: 36x36pt
  weight: .regular
```

## Glass-Morphism Components

### GlassButtonStyle
```swift
// Unified button appearance
.ultraThinMaterial background
cornerRadius: 10pt (continuous)
border: Color.primary.opacity(0.08)
padding: 12pt horizontal, 8pt vertical
font: 14pt semibold rounded

// Interaction
scaleEffect: 0.97 when pressed
animation: easeOut 0.08s
```

### Toolbar Container
```swift
// Consistent toolbar styling
.ultraThinMaterial background
cornerRadius: 14pt (continuous)
border: Color.primary.opacity(0.08), 1pt
shadow: radius 10pt, y: 4pt
padding: 10pt internal
margin: 12pt horizontal, 12pt top
```

### Floating Panel
```swift
FloatingPanelStyle:
  cornerRadius: 16pt (continuous)
  material: .regularMaterial
  shadow: black 0.15, radius 20pt, y: 8pt
  padding: 20pt margins
```

## Writer Visual System

### Editor States
```swift
// Background
.regularMaterial for editor area
Line highlight: .primary.opacity(0.05)

// Line Numbers
Color: .secondary
Font: .system(.caption, design: .monospaced)
Padding: 8pt from text
```

### Status Bar
```swift
Height: 24pt
Background: .ultraThinMaterial
Border: top 1pt, .primary.opacity(0.08)
Font: 12pt regular
Spacing: 16pt between items
```

### Font Size Controls
```swift
Range: 11-24pt
Increment: 1pt
Buttons: +/- with .secondary color
Display: Current size badge
```

## Character Sheet Visual

### Layout Modes
```swift
// Wide (>700pt)
Side-by-side sections
Fixed 350pt sidebar

// Narrow (≤700pt)
Tab-based navigation
Full-width sections
```

### Form Styling
```swift
TextField:
  .textFieldStyle(.roundedBorder)
  padding: 8pt
  
TextEditor:
  .background(Color.secondary.opacity(0.05))
  .cornerRadius(8)
  minHeight: 100pt
```

## Index Card Visual

### Card Layout
```swift
minWidth: 220pt
idealWidth: 250pt
height: 350pt
padding: 16pt
cornerRadius: 12pt
background: .regularMaterial
shadow: radius 8pt, y: 4pt
```

### Grid System
```swift
columns: floor(width / minWidth)
spacing: 20pt
animation: .spring() on resize
```

### Card States
```swift
Normal: scale(1.0), shadow(8pt)
Hover: scale(1.02), shadow(12pt)
Selected: border(2pt, .accentColor)
```

## Spacing System

### Consistent Spacing Scale
```swift
4pt   - Compact inline
6pt   - Row vertical padding
8pt   - Standard inline spacing
10pt  - Header vertical padding
12pt  - Row horizontal padding
14pt  - Internal padding
16pt  - Standard content padding
20pt  - Panel margins
```

### Platform Spacing
```swift
// macOS
Inspector width: 320pt
Tighter spacing for density

// iOS
Inspector width: 280pt
Larger touch targets
More generous spacing
```

## Animation System

### Spring Presets
```swift
// Panel transitions
.spring(response: 0.35, dampingFraction: 0.85)

// Card interactions
.spring(response: 0.3, dampingFraction: 0.8)

// Quick feedback
.spring(response: 0.2, dampingFraction: 0.9)

// Divider dragging
.interactiveSpring(response: 0.15, dampingFraction: 1)

// Button press
.easeOut(duration: 0.08)
```

### Animation Triggers
- Panel switching
- Inspector toggle
- Card selection/hover
- Button presses
- Font size changes
- Save indicator

## Material Hierarchy

### Z-Layers (Back to Front)
```
1. Application gradient background
2. Main content area
3. Editor/text areas
4. Index cards
5. Floating panels (.regularMaterial)
6. Toolbars (.ultraThinMaterial)
7. Popovers/Sheets
8. Context menus
```

### Material Usage
```swift
.ultraThinMaterial:
  - Toolbars
  - Buttons
  - Status bars
  - Labels

.regularMaterial:
  - Floating panels
  - Inspector
  - Main content areas
  - Index cards

.thinMaterial:
  - Dividers
  - Subtle overlays
```

## Icon System

### SF Symbols Configuration
```swift
// Rendering
.symbolRenderingMode(.hierarchical)

// Scaling
.imageScale(.medium)

// Weight
.font(.system(size: X, weight: .regular))
```

### Icon Categories
```swift
// Navigation
square.and.pencil, person.crop.square, square.grid.3x3

// Writing Tools
textformat.size, number, doc.text.fill

// Actions
checkmark, xmark, trash, arrow.clockwise

// File Types
doc.text.fill, folder.fill
```

## Platform Adaptations

### macOS Specific
```swift
- Hover states on all interactive elements
- Cursor changes (text, resize)
- Delete on hover
- Tighter spacing
- 320pt inspector
- Keyboard shortcuts prominent
```

### iOS Specific
```swift
- No hover states
- Touch-optimized targets (44pt minimum)
- Glass button style for menus
- Haptic feedback ready
- 280pt inspector
- Gesture-based interactions
```

### tvOS Specific
```swift
- Focus engine support
- Larger UI elements
- Simplified interactions
- 1920x1080 layout
```

## Visual Consistency Rules

### Corner Radii
```swift
16pt - Panels (continuous)
14pt - Toolbars (continuous)
12pt - Index cards (continuous)
10pt - Buttons (continuous)
8pt  - Text fields (continuous)
6pt  - Small elements (continuous)
```

### Opacity Values
```swift
0.9  - Hover states
0.85 - Normal dark mode
0.8  - Normal light mode
0.3  - Glass overlay
0.15 - Selection highlight
0.08 - Borders
0.05 - Subtle backgrounds
```

### Shadow Configuration
```swift
// Standard shadows
Panels:  20pt radius, 0.15 opacity, 8pt Y
Cards:   8pt radius, 0.12 opacity, 4pt Y
Buttons: 4pt radius, 0.15 opacity, 2pt Y

// No shadows on
Borders, dividers, inline elements
```

## Responsive Behavior

### Adaptive Layouts
```swift
// Index cards
minWidth: 220pt
columns: floor(width / minWidth)
spacing: 20pt

// Inspector
Draggable divider
minSectionHeight: 100pt
50/50 default split

// Writer
Font size: 11-24pt adaptive
Line numbers: toggle visibility
```

### Breakpoints
```swift
// Character sheet
width > 700pt: Side-by-side
width ≤ 700pt: Tabs

// General panels
Maintain fixed widths
Content adapts internally
```

## Accessibility

### Implemented
- Semantic colors for color scheme adaptation
- `.help()` tooltips on buttons
- Standard SwiftUI accessibility
- Sufficient contrast ratios
- Adjustable font sizes (11-24pt)

### Planned
- Dynamic Type support
- Reduced motion alternatives
- High contrast mode
- VoiceOver optimization
- Keyboard navigation complete

## Design Tokens

### Reusable Styles
```swift
GlassButtonStyle()       // All buttons
FloatingPanelStyle()     // All panels
ToolbarStyle.platform    // All toolbars
```

### Consistent Patterns
- All interactive elements scale on press
- All panels use continuous corners
- All materials have subtle borders
- All animations use spring timing

## Quality Standards

### Visual Polish
✅ Consistent glass-morphism
✅ Smooth animations
✅ Platform-appropriate styling
✅ State-based feedback
✅ Modern aesthetic

### Areas for Enhancement
- Loading states
- Empty states
- Error states
- Success feedback
- Skeleton screens
- Save indicators

## Performance Considerations

### Optimizations
- Minimal shadow layers
- Efficient material usage
- Cached gradients
- Lazy view rendering

### Visual Performance
- 60fps animations
- < 16ms render time
- Smooth state transitions
- No visual glitches
- Fast text rendering