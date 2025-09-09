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

### Canvas Background
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

### Node Type Colors
```swift
// Story Category (Blue/Purple)
.character: .blue
.dialogue: .purple
.cutscene: .indigo
.choice: .pink

// World Category (Green)
.location: .green
.region: .mint
.landmark: .teal

// Gameplay Category (Orange/Red)
.quest: .orange
.objective: .yellow
.combat: .red
.puzzle: .cyan
.item: .yellow
.weapon: .red
.keyItem: .orange

// System Category (Gray)
.trigger: .gray
.condition: .brown
.variable: .secondary
.event: .primary
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

## Node Visual System

### Node States & Opacity
```swift
// Background opacity by state
Normal:    0.85 (dark) / 0.8 (light)
Hovered:   0.9
Selected:  0.85
Dragging:  0.95

// Border width by state
Normal:    1.0pt
Hovered:   1.5pt
Selected:  2.5pt
Dragging:  2.5pt
```

### Node Shadows
```swift
// Shadow radius by state
Normal:    5pt
Hovered:   8pt
Selected:  10pt
Dragging:  15pt

// Shadow Y offset
Normal:    2pt
Hovered:   4pt
Dragging:  8pt

// Shadow color
Type color with opacity (0.2-0.4)
```

### Node Scaling
```swift
// Scale effect by state
Normal:    1.0
Hovered:   1.03
Dragging:  1.08
```

### Display Modes
```swift
Compact Mode:
  - Solid color background
  - 33pt icon
  - 8pt padding
  - No content preview

Normal Mode:
  - Glass effect + color
  - 14-18pt icons
  - 12/14pt padding
  - Shows header & tags

Expanded Mode:
  - Same as normal
  - Full content visible
```

## Spacing System

### Consistent Spacing Scale
```swift
4pt   - Compact inline
6pt   - Row vertical padding
8pt   - Standard inline spacing
10pt  - Header vertical padding
12pt  - Row horizontal padding
14pt  - Node internal padding
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

// Node interactions
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
- Node selection/hover
- Drag operations
- Button presses
- Zoom snapping

## Material Hierarchy

### Z-Layers (Back to Front)
```
1. Canvas gradient background
2. Grid overlay (optional)
3. Edge connections
4. Nodes with glass effect
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
  - Node badges
  - Labels

.regularMaterial:
  - Floating panels
  - Inspector
  - Main content areas

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
sidebar.right, folder.fill, plus.circle

// Node Types (19 icons)
person.fill, map.fill, flag.fill, etc.

// Actions
checkmark, xmark, trash, arrow.clockwise

// File Types
doc.text.fill, folder.fill
```

## Platform Adaptations

### macOS Specific
```swift
- Hover states on all interactive elements
- Cursor changes (resize, arrow)
- Delete on hover
- Tighter spacing
- 320pt inspector
```

### iOS Specific
```swift
- No hover states
- Touch-optimized targets (44pt minimum)
- Glass button style for menus
- Haptic feedback ready
- 280pt inspector
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
10pt - Buttons (continuous)
8-16pt - Nodes (by type, continuous)
6pt - Small elements (continuous)
```

### Opacity Values
```swift
0.95 - Maximum opacity (dragging)
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
Nodes:   5-15pt radius, 0.12-0.4 opacity, 2-8pt Y
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

### Planned
- Dynamic Type support
- Reduced motion alternatives
- High contrast mode
- VoiceOver optimization

## Design Tokens

### Reusable Styles
```swift
GlassButtonStyle()       // All buttons
NodeStyle()              // All nodes
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