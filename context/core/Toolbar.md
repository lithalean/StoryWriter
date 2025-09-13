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

Views/Project/
├── ProjectToolbar.swift        # Main navigation UI
├── ProjectToolbarConfig.swift  # Navigation behaviors
└── ProjectState.swift          # Section enum (3 panels)

Views/Writer/
├── WriterToolbar.swift         # Editor toolbar UI
└── WriterToolbarConfig.swift   # Editor behaviors
```

## Implementation

### ProjectToolbar

Main navigation and project management.

**Config Structure:**
- `isInspectorVisible/toggleSidebar`: Inspector panel control
- `newProject`: Create new project
- `undo`: Undo last action (not implemented)
- `export`: Export project (not implemented)
- `selectedSection/selectSection`: Panel navigation

**Panels (3 sections):**
```swift
selectedCenterButton:
  0 → WriterWindow (pen icon)
  1 → CharacterSheet (person icon)  
  2 → IndexWindow (grid icon)
```

**Features:**
- Single-window panel switching
- Inspector toggle with animation
- Glass morphism styling
- Platform-adaptive layout

### WriterToolbar

Text editor controls with comprehensive writing tools.

**Config Structure (Simplified):**
```swift
struct WriterToolbarConfig {
    var fontSize: CGFloat
    var setFontSize: (CGFloat) -> Void
    var incrementFontSize: () -> Void
    var decrementFontSize: () -> Void
    var showLineNumbers: Bool
    var toggleLineNumbers: () -> Void
    var wordCount: Int
    var lineCount: Int
    var isDirty: Bool
}
```

**Features:**
- Font size controls (11-24pt range)
  - Increment/decrement buttons (+/-)
  - Current size display
- Line numbers toggle
- Word count display
- Line count display
- Unsaved changes indicator (isDirty)

**Implementation Details:**
```swift
// Font size bounds enforced in WriterState
func incrementFontSize() {
    fontSize = min(24, fontSize + 1)
}

func decrementFontSize() {
    fontSize = max(11, fontSize - 1)
}
```

## Styling

### GlassButtonStyle

Shared button style with glass morphism effect:
- Ultra-thin material background
- 10pt corner radius (continuous)
- Press animation (0.97 scale)
- 0.08 opacity border
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
- 14pt corner radius (continuous)
- Ultra-thin material background
- 0.08 opacity border
- Shadow with 10pt radius, 4pt Y offset
- 12pt horizontal margin, 12pt top margin

## Platform Support

### macOS
- Native styling
- Hover states enabled
- Compact spacing
- Keyboard shortcut hints

### iOS/iPadOS  
- Glass button style for controls
- 44pt minimum touch targets
- Haptic feedback ready
- Adaptive layouts

### tvOS
- iOS styling adapted
- Focus engine support
- Remote control optimized

## State Management

### Config Pattern Example (Writer)

```swift
// Parent owns state
@StateObject private var state = WriterState()
@StateObject private var document = WriterDocument()

// Config provides behaviors
private var toolbarConfig: WriterToolbarConfig {
    WriterToolbarConfig(
        fontSize: state.fontSize,
        setFontSize: { state.fontSize = $0 },
        incrementFontSize: { state.incrementFontSize() },
        decrementFontSize: { state.decrementFontSize() },
        showLineNumbers: state.showLineNumbers,
        toggleLineNumbers: { state.showLineNumbers.toggle() },
        wordCount: state.wordCount,
        lineCount: state.lineCount,
        isDirty: document.isDirty
    )
}

// Toolbar receives config
WriterToolbar(config: toolbarConfig)
```

### No Direct State

Toolbars never contain:
- `@State`
- `@StateObject`
- `@ObservedObject`
- `@EnvironmentObject`
- `@Binding` (except in previews)

## Preview Support

All toolbars include platform previews using `StatefulPreviewWrapper`:

```swift
#Preview("macOS") {
    StatefulPreviewWrapper(WriterToolbarConfig.mock) { config in
        WriterToolbar(config: config)
            .toolbarStyle(.macOS)
    }
}
```

## Testing

### Unit Testing

Config behaviors are pure functions:
```swift
func testFontSizeIncrement() {
    var fontSize: CGFloat = 14
    let config = WriterToolbarConfig(
        incrementFontSize: { 
            fontSize = min(24, fontSize + 1)
        }
    )
    config.incrementFontSize()
    XCTAssertEqual(fontSize, 15)
}

func testFontSizeBounds() {
    // Test upper bound
    var fontSize: CGFloat = 24
    config.incrementFontSize()
    XCTAssertEqual(fontSize, 24) // Should not exceed
    
    // Test lower bound
    fontSize = 11
    config.decrementFontSize()
    XCTAssertEqual(fontSize, 11) // Should not go below
}
```

### Integration Testing

- Toolbar appearance across platforms
- Animation smoothness
- Touch/click targets
- Accessibility labels
- State synchronization

## Best Practices

### DO
- Keep toolbars stateless
- Use configs for all behavior
- Maintain consistent styling
- Include platform previews
- Test behavior contracts
- Enforce bounds in state objects

### DON'T
- Add state to toolbars
- Access stores directly
- Override glass styling
- Forget accessibility
- Make toolbars too tall
- Put business logic in configs

## Accessibility

### Current Support
- `.help()` tooltips on buttons
- Semantic button labels
- Standard SwiftUI accessibility

### Planned Improvements
- VoiceOver optimization
- Keyboard shortcuts
- Focus indicators
- Custom accessibility actions

## Future Enhancements

- [ ] Save/Open buttons in WriterToolbar
- [ ] Undo/Redo functionality
- [ ] Export options
- [ ] Find/Replace controls
- [ ] Markdown preview toggle
- [ ] Theme selection
- [ ] Keyboard shortcuts display
- [ ] Customizable toolbar layouts