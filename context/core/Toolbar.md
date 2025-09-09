# Toolbar System

## Overview

StoryWriter uses a unified toolbar architecture across all major views. The system implements a behavior-contract pattern where toolbars are stateless UI components that receive all functionality through configuration structs.

## Architecture

### Core Pattern

```
Window/View (owns state)
    ↓
Config Struct (behavior contract)
    ↓
Toolbar View (stateless UI)
```

### Key Files

```
Sources/Shared/
├── GlassButtonStyle.swift      # Unified button styling
└── ToolbarStyle.swift          # Platform adaptations

Views/Mapper/
├── MapperToolbar.swift         # Canvas toolbar UI
└── MapperToolbarConfig.swift   # Canvas behaviors

Views/Project/
├── ProjectToolbar.swift        # Project toolbar UI
├── ProjectToolbarConfig.swift  # Project behaviors
└── ProjectState.swift          # Section navigation enum

Views/Writer/
├── WriterToolbar.swift         # Editor toolbar UI
└── WriterToolbarConfig.swift   # Editor behaviors
```

## Implementation

### MapperToolbar

Canvas controls for the node-based story mapper.

**Config Structure:**
- `zoomIn/zoomOut`: Scale adjustment (0.1x to 6.0x)
- `reset`: Return to default view
- `toggleGrid/isGridOn`: Grid visibility
- `toggleSnap/isSnapOn`: Snap-to-grid behavior
- `pickType`: Node type selection from palette

**Features:**
- Zoom range: 0.1x to 6.0x with 15% increments
- Grid overlay toggle
- Snap-to-grid (20pt grid)
- Node palette menu with categories

**Node Palette Integration:**
```swift
let nodePalette: NodePalette
// Provides categories and types for Add menu
```

### ProjectToolbar

Main navigation and project management.

**Config Structure:**
- `isInspectorVisible/toggleSidebar`: Inspector panel control
- `newProject`: Create new project
- `undo`: Undo last action
- `export`: Export project
- `selectedSection/selectSection`: Section navigation

**Sections (ProjectState):**
- `storyFiles`: Document management
- `characters`: Character sheets
- `locations`: Location management  
- `timeline`: Timeline view

### WriterToolbar

Markdown editor controls.

**Config Structure:**
- `fontSize/setFontSize`: Font size control (11-24pt)
- `clearText`: Clear document
- `wordCount`: Word count telemetry

**Features:**
- Font size slider with 1pt steps
- Live word count display
- Clear document action
- Minimal height design

## Styling

### GlassButtonStyle

Shared button style with glass morphism effect:
- Ultra-thin material background
- 8pt corner radius
- Press animation (0.95 scale)
- Consistent across all toolbars

### ToolbarStyle

Platform-specific adaptations:
```swift
enum ToolbarStyle {
    case macOS  // Compact, hover states
    case iOS    // Touch targets, haptics
}
```

### Container Styling

All toolbars share:
- 10pt internal padding
- 14pt corner radius
- Ultra-thin material background
- 0.08 opacity border
- Shadow with 10pt radius, 4pt Y offset
- 12pt horizontal margin, 12pt top margin

## Platform Support

### macOS
- Native menu styling
- Hover states enabled
- Compact spacing

### iOS/iPadOS  
- Glass button style for menus
- Enlarged touch targets
- Haptic feedback

### tvOS
- iOS styling adapted
- Focus engine support
- Remote control optimized

## State Management

### Config Pattern

```swift
// Parent owns state
@StateObject private var state = MapperState()

// Config provides behaviors
private var toolbarConfig: MapperToolbarConfig {
    MapperToolbarConfig(
        zoomIn: { state.scale *= 1.15 },
        zoomOut: { state.scale *= 0.85 }
    )
}

// Toolbar receives config
MapperToolbar(config: toolbarConfig)
```

### No Direct State

Toolbars never contain:
- `@State`
- `@StateObject`
- `@ObservedObject`
- `@EnvironmentObject`

## Preview Support

All toolbars include platform previews using `StatefulPreviewWrapper`:

```swift
#Preview("macOS") {
    StatefulPreviewWrapper(initialValue) { binding in
        Toolbar(config: mockConfig)
            .toolbarStyle(.macOS)
    }
}
```

## Testing

### Unit Testing

Config behaviors are pure functions:
```swift
func testZoom() {
    var scale = 1.0
    let config = MapperToolbarConfig(
        zoomIn: { scale *= 1.15 }
    )
    config.zoomIn()
    XCTAssertEqual(scale, 1.15)
}
```

### Integration Testing

- Toolbar appearance across platforms
- Animation smoothness
- Touch/click targets
- Accessibility labels

## Best Practices

### DO
- Keep toolbars stateless
- Use configs for all behavior
- Maintain consistent styling
- Include platform previews
- Test behavior contracts

### DON'T
- Add state to toolbars
- Access stores directly
- Override glass styling
- Forget accessibility
- Make toolbars too tall

## Future Enhancements

- [ ] Customizable layouts
- [ ] Keyboard shortcuts
- [ ] Context tooltips
- [ ] Toolbar presets
- [ ] Collapsible sections