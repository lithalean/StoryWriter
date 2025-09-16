# Toolbar System Documentation

**Module Version**: 3.0.0  
**Status**: Production Ready  
**Completeness**: 95%  
**Last Updated**: September 2025  
**Pattern**: Behavior-Contract Architecture

## Overview

StoryWriter uses a unified toolbar architecture with a single ProjectToolbar handling all main controls. The system implements a behavior-contract pattern where the toolbar is a stateless UI component that receives all functionality through a configuration struct.

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

Project/
├── ProjectToolbar.swift        # Main unified toolbar UI
├── ProjectToolbarConfig.swift  # Toolbar behaviors
└── ProjectState.swift          # Section enum (2 modes)
```

**Important**: WriterToolbar.swift and WriterToolbarConfig.swift were removed in the September 2025 refactor. All functionality has been consolidated into ProjectToolbar.

## Implementation

### ProjectToolbar (Unified)

The single toolbar managing all application controls.

**Layout Structure:**
```
[Sidebar Toggle] [New Project] | [Writer] [Reader] | [Save] [Formatter Toggle]
     Left Controls              Center Navigation        Right Controls
```

### ProjectToolbarConfig

The behavior contract defining all toolbar functionality:

**Left Side Controls:**
```swift
isInspectorVisible: () -> Bool      // Current inspector state
toggleSidebar: () -> Void          // Toggle inspector panel
newProject: () -> Void             // Create new project (placeholder)
undo: () -> Void                   // Undo action (not implemented)
```

**Center Navigation:**
```swift
selectedSection: () -> ProjectState  // Current mode (Writer/Reader)
selectSection: (ProjectState) -> Void // Switch modes
```

**Right Side Controls:**
```swift
isRightPanelVisible: () -> Bool     // Formatter panel state
toggleRightPanel: () -> Void       // Toggle formatter panel
isDirty: () -> Bool                // Document has unsaved changes
save: () -> Void                   // Trigger save action
canSave: () -> Bool                // Save button enabled state
```

### ProjectState

Simplified navigation enum (reduced from 3+ panels):

```swift
public enum ProjectState {
    case writer  // Document editing mode (doc icon)
    case reader  // Reading mode (book icon)
}
```

## Visual Design

### Button Styling

All buttons use `GlassButtonStyle`:
- Ultra-thin material background
- 10pt corner radius (continuous)
- Press animation (0.97 scale)
- 0.08 opacity border
- Consistent hover states

### Toolbar Container

```swift
// Specifications
- Height: 52pt fixed
- Padding: 12pt horizontal, 6pt vertical
- Corner radius: 14pt (continuous)
- Background: Ultra-thin material
- Border: 0.08 opacity primary color
- Shadow: 10pt radius, 4pt Y offset
- Margins: 8pt horizontal, 4pt top
```

### Icons and Indicators

**Navigation Icons:**
- Writer: "doc" (document icon)
- Reader: "book" (book icon)

**Control Icons:**
- Inspector: "sidebar.right" / "sidebar.left"
- New Project: "doc.badge.plus"
- Save: "opticaldiscdrive" / "opticaldiscdrive.fill"
- Formatter: "sidebar.right" / "sidebar.left"

**Visual States:**
- Selected mode: Blue tint
- Unselected: Secondary color
- Dirty document: Filled save icon with accent color
- Clean document: Outline save icon with secondary color

## Animations

### Spring Animations
```swift
.spring(response: 0.35, dampingFraction: 0.85)
```
Applied to:
- Inspector toggle
- Formatter toggle
- Mode switching

## Platform Support

### macOS
- Compact spacing
- Hover states enabled
- Keyboard shortcut hints
- Native styling

### iOS/iPadOS
- Touch-optimized targets
- Glass button style
- Haptic feedback ready
- Adaptive layouts

### tvOS
- Focus engine support
- Remote control optimized
- Larger touch targets
- iOS styling adapted

## State Management

### Integration Example

```swift
struct ContentView: View {
    @State private var showInspector = true
    @State private var showFormatter = false
    @State private var currentSection: ProjectState = .writer
    @StateObject private var document = WriterDocument()
    
    var body: some View {
        VStack {
            ProjectToolbar(config: ProjectToolbarConfig(
                isInspectorVisible: { showInspector },
                toggleSidebar: { showInspector.toggle() },
                newProject: { /* TODO */ },
                undo: { /* TODO */ },
                selectedSection: { currentSection },
                selectSection: { currentSection = $0 },
                isRightPanelVisible: { showFormatter },
                toggleRightPanel: { showFormatter.toggle() },
                isDirty: { document.isDirty },
                save: { document.save() },
                canSave: { document.isDirty }
            ))
            
            // Content based on currentSection
            switch currentSection {
            case .writer:
                WriterWindow()
            case .reader:
                ReaderWindow()
            }
        }
    }
}
```

### No Internal State

The toolbar never contains:
- `@State`
- `@StateObject`
- `@ObservedObject`
- `@EnvironmentObject`
- `@Binding` (except in previews)

All state is owned by the parent view and passed through closures.

## Save Integration

### Save Button Behavior

```swift
// Visual indication
Icon: isDirty() ? "opticaldiscdrive.fill" : "opticaldiscdrive"
Color: isDirty() ? .accentColor : .secondary

// Interaction
Action: save()
Enabled: canSave() // Usually same as isDirty()
Help: "Save project (⌘S)"
```

### Save Flow
1. User edits document → `isDirty` becomes true
2. Save button shows filled icon with accent color
3. User clicks save or presses ⌘S
4. `save()` closure triggers `document.save()`
5. Document saves, `isDirty` becomes false
6. Save button returns to outline style

## Font Controls Migration

Font size controls previously in WriterToolbar have moved to:
1. **FormatterPanel**: Primary location for font controls
2. **Keyboard shortcuts**: Future implementation
3. **Writer preferences**: Planned settings panel

This consolidation simplified the toolbar and created clearer separation between navigation (toolbar) and formatting (formatter panel).

## Testing

### Unit Testing

```swift
func testSaveButtonState() {
    var isDirty = false
    var saveCount = 0
    
    let config = ProjectToolbarConfig(
        // ... other closures
        isDirty: { isDirty },
        save: { saveCount += 1 },
        canSave: { isDirty }
    )
    
    // Test clean state
    XCTAssertFalse(config.canSave())
    
    // Test dirty state
    isDirty = true
    XCTAssertTrue(config.canSave())
    
    // Test save action
    config.save()
    XCTAssertEqual(saveCount, 1)
}
```

### Integration Testing

- Mode switching animation
- Panel toggle synchronization
- Save state visual updates
- Platform-specific behaviors
- Keyboard shortcut handling

## Accessibility

### Current Support
- `.help()` tooltips on all buttons
- Semantic labels for VoiceOver
- Keyboard navigation support
- High contrast borders

### Future Improvements
- Custom accessibility actions
- VoiceOver optimization
- Enhanced focus indicators
- Keyboard shortcut badges

## Best Practices

### DO
- Keep toolbar stateless
- Use config for all behaviors
- Maintain visual consistency
- Test behavior contracts
- Include platform previews
- Use spring animations

### DON'T
- Add state to toolbar
- Access stores directly
- Override glass styling
- Forget accessibility
- Mix navigation with formatting
- Put business logic in toolbar

## Comparison: Before and After

### Before (Dual Toolbars)
- ProjectToolbar: Navigation only
- WriterToolbar: Font size, line numbers, word count
- Complexity: Two configs to manage
- Confusion: Which toolbar for what?

### After (Unified Toolbar)
- ProjectToolbar: All primary controls
- FormatterPanel: Text formatting
- Simplicity: Single toolbar config
- Clarity: Navigation vs formatting

## Known Limitations

1. **Undo not implemented**: Button exists but non-functional
2. **New Project placeholder**: Creates blank document only
3. **No export button**: Planned for future
4. **No keyboard badges**: Shortcuts work but not shown

## Future Enhancements

### Near Term
- [ ] Implement undo/redo functionality
- [ ] Add export options menu
- [ ] Show keyboard shortcut badges
- [ ] New project templates

### Medium Term
- [ ] Customizable toolbar layouts
- [ ] Quick access formatting buttons
- [ ] Document tabs
- [ ] Search functionality

### Long Term
- [ ] Toolbar profiles
- [ ] Cloud sync status
- [ ] Collaboration indicators
- [ ] Plugin system

## Migration Guide

### For Developers

If updating from pre-September 2025:

1. **Remove WriterToolbar references**
   ```swift
   // Old
   WriterToolbar(config: writerConfig)
   
   // New
   ProjectToolbar(config: projectConfig)
   ```

2. **Move font controls to FormatterPanel**
   ```swift
   // Font size, line numbers now in FormatterPanel
   FormatterPanel()
   ```

3. **Update save integration**
   ```swift
   // Save now in ProjectToolbar right side
   isDirty: { document.isDirty },
   save: { document.save() }
   ```

## Success Metrics

### Current Achievement (95%)
- ✅ Unified toolbar system
- ✅ Save integration complete
- ✅ Mode switching smooth
- ✅ Panel toggles working
- ✅ Behavior-contract pattern
- ✅ Platform support
- ❌ Undo/redo not implemented

The toolbar system represents one of StoryWriter's most polished components, demonstrating clean architecture and thoughtful design consolidation.