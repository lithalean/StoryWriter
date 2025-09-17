# StoryWriter Navigation & Layout System

**Version**: 3.1.0  
**Architecture**: Single-window with precision layout  
**Pattern**: Behavior-contract navigation with calculated positioning  
**Platform Support**: macOS, iOS, tvOS  
**Last Updated**: October 2025

## Layout Architecture

### Precision Layout System

StoryWriter uses a sophisticated layout calculation system to ensure perfect alignment of all UI elements. The system accounts for toolbar height, statusbar height, padding requirements, and fine-tuning offsets.

```
Layout Constants (from ContentView.swift):
├── toolbarHeight: 52pt
├── statusbarHeight: 36pt  
├── topToolbarPadding: 8pt
├── bottomStatusbarPadding: 8pt
└── floatingPanelYOffset: 6pt  // Fine-tuning offset
```

## Critical Layout Calculations

### Available Height Calculation

The available height for content is calculated by subtracting all UI chrome from the total window height:

```swift
let availableHeight = geometry.size.height 
    - toolbarHeight           // 52pt
    - statusbarHeight         // 36pt
    - topToolbarPadding      // 8pt
    - bottomStatusbarPadding  // 8pt
```

This ensures content never overlaps with the toolbar or statusbar.

### Window Y-Offset Calculation

The main content window needs vertical centering between toolbar and statusbar:

```swift
let windowYOffset = ((toolbarHeight + topToolbarPadding) - (statusbarHeight + bottomStatusbarPadding)) / 2 
    + floatingPanelYOffset

// Breaking this down:
// 1. Top space = toolbarHeight (52) + topPadding (8) = 60pt
// 2. Bottom space = statusbarHeight (36) + bottomPadding (8) = 44pt  
// 3. Difference = 60 - 44 = 16pt
// 4. Center offset = 16 / 2 = 8pt
// 5. Final offset = 8 + floatingPanelYOffset (6) = 14pt
```

The `floatingPanelYOffset` of 6pt is a fine-tuning value that slightly lowers panels for better visual balance.

### Panel Height Calculation

Side panels (Inspector and Formatter) have their own height calculation:

```swift
let availableHeight = geometry.size.height 
    - toolbarHeight     // 52pt
    - statusbarHeight   // 36pt
    - 8                 // Top padding
    - 8                 // Bottom padding
```

Panels are then positioned with:
- `.padding(.top, toolbarHeight)` - Ensures they start below toolbar
- `.padding(.bottom, statusbarHeight)` - Ensures they end above statusbar
- `.offset(y: calculatedOffset)` - Applies the same Y-offset as main content

## Layout Stack Structure

### Z-Layer Organization

```
ZStack {
    // Layer 0: Background
    AppBackground()
        .ignoresSafeArea()
    
    // Layer 1: Main Content Area
    GeometryReader { geometry in
        Group {
            // Writer or Reader window
        }
        .floatingPanelStyle()
        .frame(maxHeight: availableHeight)
        .offset(y: windowYOffset)  // 14pt down
    }
    
    // Layer 2: Side Panels
    GeometryReader { geometry in
        if showInspector { /* Left panel */ }
        if showFormatter { /* Right panel */ }
    }
    
    // Layer 3: Chrome Overlay
    VStack {
        ProjectToolbar()
            .padding(.top, 8)     // topToolbarPadding
        Spacer()
        ProjectStatusbar()
            .padding(.bottom, 8)  // bottomStatusbarPadding
    }
}
```

## Panel Positioning Logic

### Inspector Panel (Left)

```swift
HStack {
    InspectorPanel()
        .floatingPanelStyle()
        .frame(width: 280)
        .frame(maxHeight: availableHeight)
        .padding(.leading, 20)
        .padding(.top, toolbarHeight)      // 52pt
        .padding(.bottom, statusbarHeight) // 36pt
    Spacer()
}
.offset(y: windowYOffset)  // 14pt vertical offset
```

### Formatter Panel (Right)

```swift
HStack {
    Spacer()
    FormatterPanel()
        .floatingPanelStyle()
        .frame(width: 280)
        .frame(maxHeight: availableHeight)
        .padding(.trailing, 20)
        .padding(.top, toolbarHeight)      // 52pt
        .padding(.bottom, statusbarHeight) // 36pt
}
.offset(y: windowYOffset)  // 14pt vertical offset
```

### Main Content Window

```swift
Group {
    switch currentSection {
    case .writer: WriterWindow()
    case .reader: ReaderWindow()
    }
}
.floatingPanelStyle()
.padding(.vertical, 20)
.padding(.horizontal, 20)
.frame(maxHeight: availableHeight)
.offset(y: windowYOffset)  // 14pt vertical offset
```

## Spacing and Padding System

### Content Padding
- **Horizontal**: 20pt on all sides for main content and panels
- **Vertical**: 20pt for content windows

### Chrome Padding
- **Toolbar**: 8pt top padding
- **Statusbar**: 8pt bottom padding
- **Toolbar horizontal**: Full width minus horizontal padding

### Panel Spacing
- **Width**: 280pt (iOS/tvOS), 320pt (macOS)
- **Gap from edge**: 20pt
- **Gap from content**: Implicit through layout

## Animation System

### Panel Toggle Animations

All panel toggles use consistent spring animations:

```swift
.animation(.spring(response: 0.35, dampingFraction: 0.85), value: showInspector)
.animation(.spring(response: 0.35, dampingFraction: 0.85), value: showFormatter)
```

### Mode Switching Animation

Content mode transitions use faster springs:

```swift
withAnimation(.spring(response: 0.3, dampingFraction: 0.85)) {
    currentSection = section
}
```

## State Management

### Panel State Variables

```swift
@State private var showInspector = true
@State private var showFormatter = false
@State private var currentSection: ProjectState = .writer
```

### Toggle Actions

Inspector toggle:
```swift
toggleSidebar: {
    withAnimation(.spring(response: 0.35, dampingFraction: 0.85)) {
        showInspector.toggle()
    }
}
```

Formatter toggle:
```swift
toggleRightPanel: {
    withAnimation(.spring(response: 0.35, dampingFraction: 0.85)) {
        showFormatter.toggle()
    }
}
```

## Visual Alignment Verification

### Toolbar-Content Alignment
- Toolbar bottom edge: `8 + 52 = 60pt` from top
- Content top with offset: `20 + 14 = 34pt` from top
- Visual gap: `60 - 34 = 26pt`

### Content-Statusbar Alignment
- Content bottom: `windowHeight - (20 + availableHeight + 14)`
- Statusbar top: `windowHeight - (8 + 36)`
- Ensures no overlap with proper spacing

### Panel Alignment
- Panels start at: `toolbarHeight (52pt)` from top
- Panels end at: `statusbarHeight (36pt)` from bottom
- Y-offset ensures vertical centering: `14pt`

## Platform-Specific Adjustments

### macOS
- Panel width: 320pt (inspector and formatter)
- Window minimum size constraints apply
- Native scrolling behavior

### iOS/iPadOS
- Panel width: 280pt
- Touch-safe margins
- Safe area considerations

### tvOS
- Fixed 1920x1080 layout
- Focus-safe zones
- Panel width: 280pt

## Responsive Behavior

### Window Resize Handling

The GeometryReader recalculates on window resize:
1. New `availableHeight` computed
2. Window and panels adjust their `maxHeight`
3. Y-offset remains constant (14pt)
4. Panels maintain fixed width

### Panel Toggle Impact
- Main content maintains position
- No reflow of central content
- Smooth spring animations
- Fixed chrome positions

## Layout Debugging

### Visual Verification Points

To verify correct layout:
1. **Toolbar clearance**: Content should have visible gap below toolbar
2. **Statusbar clearance**: Content should end above statusbar
3. **Panel alignment**: Panels should align with content vertically
4. **Offset consistency**: All elements at same Y-offset (14pt)

### Common Issues and Solutions

**Problem**: Content overlaps toolbar
**Solution**: Verify `availableHeight` calculation includes all padding

**Problem**: Panels appear misaligned
**Solution**: Ensure same `windowYOffset` applied to all elements

**Problem**: Bottom content cut off
**Solution**: Check statusbar height and bottom padding in calculations

## Mathematical Precision

### Key Formula

```
Final Y-Offset = ((ToolbarSpace - StatusbarSpace) / 2) + FineTuning

Where:
- ToolbarSpace = toolbarHeight + topPadding
- StatusbarSpace = statusbarHeight + bottomPadding  
- FineTuning = floatingPanelYOffset (6pt)
```

### Validation
- Total chrome height: 52 + 36 + 8 + 8 = 104pt
- Available content: windowHeight - 104pt
- Vertical centering offset: 14pt
- Ensures balanced visual weight

## Performance Considerations

### Layout Calculations
- Performed once per window resize
- Cached in local constants
- No repeated calculations in render

### Animation Performance
- Spring animations GPU-accelerated
- Fixed durations (0.35s panels, 0.3s mode)
- No layout thrashing

## Best Practices

### DO
- Use the calculated `availableHeight` for all content
- Apply consistent `windowYOffset` to aligned elements
- Maintain padding constants in single location
- Test with various window sizes

### DON'T
- Hard-code positions without calculations
- Skip the Y-offset for aligned elements
- Use different animation timings
- Ignore platform-specific panel widths

## Testing Checklist

- [ ] Toolbar has 8pt top padding
- [ ] Statusbar has 8pt bottom padding  
- [ ] Content respects availableHeight
- [ ] All elements use 14pt Y-offset
- [ ] Panels align with main content
- [ ] No overlap between layers
- [ ] Animations smooth at 60fps
- [ ] Resize maintains alignment

The precision layout system ensures pixel-perfect alignment across all UI elements while maintaining flexibility for different window sizes and platform requirements.