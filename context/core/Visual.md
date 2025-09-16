# StoryWriter Visual Design System

**Version**: 3.0.0  
**Design Language**: Glass-Morphism with Paper Document Metaphor  
**Theme**: Dark-First with Light Mode Support  
**Architecture**: Unified Component Styling  
**Last Updated**: September 2025

## Design Philosophy

StoryWriter implements a sophisticated **three-layer visual hierarchy** combining glass-morphism UI with a paper document metaphor for the writing surface, creating clear separation between interface and content.

### Core Principles
1. **Paper Document Metaphor**: Writing surface as physical paper
2. **Glass-Morphism UI**: Controls and panels use materials
3. **Visual Hierarchy**: Canvas → Paper → Controls
4. **Platform Harmony**: Native feel on each platform

## Visual Hierarchy

### Three-Layer System

```
Layer 1: Canvas Background (AppBackground)
    ↓
Layer 2: Paper Document (WriterWindow)
    ↓
Layer 3: UI Overlays (Toolbar, Statusbar, Panels)
```

### Paper Document Design

```swift
// WriterWindow Paper Appearance
Background: Color(white: 0.98) // Off-white paper
ColorScheme: .light (forced)   // Always light
CornerRadius: 16pt (continuous)
Shadow: black 0.2 opacity, 20pt radius, 10pt Y
Padding: 20pt horizontal, 20pt top, 30pt bottom

// Paper Texture
LinearGradient(
    colors: [
        Color(white: 1.0, opacity: 0.5),
        Color(white: 0.95, opacity: 0.3)
    ],
    startPoint: .topLeading,
    endPoint: .bottomTrailing
)
```

## Typography System

### Writing Modes

```swift
// Writer Mode (Editing)
Font: .system(size: 15, design: .monospaced)
Range: 11-24pt (user adjustable)
Line Height: 1.4
Color: .primary on paper background

// Reader Mode (Reading)
Font: .system(size: 18, design: .serif)
Range: 12-28pt (wider for accessibility)
Line Height: 1.6
Color: .primary on canvas

// UI Elements
Toolbar: .system(size: 13)
Statusbar: .caption
Formatter: .system(size: 14)
```

## Component Visual Updates

### Unified ProjectToolbar

```swift
// Container (consolidated design)
Height: 52pt fixed
Background: .ultraThinMaterial
CornerRadius: 14pt (continuous)
Border: Color.primary.opacity(0.08), 1pt
Shadow: radius 10pt, y: 4pt
Padding: 12pt horizontal, 6pt vertical
Margin: 8pt horizontal, 4pt top

// Button Layout
Left: [Sidebar Toggle] [New Project]
Center: [Writer] [Reader]
Right: [Save] [Formatter Toggle]

// Save Button States
Clean: "opticaldiscdrive" with .secondary
Dirty: "opticaldiscdrive.fill" with .accentColor
```

### ProjectStatusbar

```swift
// Position and Style
Location: Bottom overlay
Height: ~30pt
Background: .ultraThinMaterial with blur
Typography: .caption size
Color: .secondary for subtlety
Padding: 8pt horizontal, 4pt vertical

// Information Modules
[Section] | Words: 1,234 | Draft | Saved 2 min ago | [⚙]
```

### Formatter Panel

```swift
// Right Sidebar Design
Width: 280pt (iOS), 320pt (macOS)
Background: .regularMaterial
CornerRadius: 16pt (continuous)
Shadow: black 0.15, radius 20pt, y: 8pt

// Controls
Font Size Slider: 11-24pt range
Format Buttons: Bold, Italic, Underline
Alignment: Left, Center, Right, Justify
Line Numbers: Toggle switch
```

## Color System

### Canvas Background (AppBackground)

```swift
// Dark Mode (Primary)
ZStack {
    LinearGradient(
        colors: [Color(white: 0.05), Color(white: 0.02)],
        startPoint: .topLeading,
        endPoint: .bottomTrailing
    )
    
    // Noise texture overlay
    Image("noise")
        .opacity(0.02)
        .blendMode(.plusLighter)
}

// Light Mode
LinearGradient(
    colors: [Color(white: 0.95), Color(white: 0.92)],
    startPoint: .topLeading,
    endPoint: .bottomTrailing
)
```

### Semantic Colors

```swift
// Status Indicators
.dirty: .accentColor (blue)
.saved: .secondary
.saving: .yellow
.error: .red

// Panel Backgrounds
Inspector: .regularMaterial
Formatter: .regularMaterial
Paper: Color(white: 0.98)
```

## Glass-Morphism Components

### GlassButtonStyle (Unchanged)

```swift
// Consistent across all buttons
Background: .ultraThinMaterial
CornerRadius: 10pt (continuous)
Border: Color.primary.opacity(0.08)
ScaleEffect: 0.97 when pressed
Animation: .easeOut(duration: 0.08)
```

### Floating Panel Style

```swift
// Inspector and Formatter Panels
func floatingPanelStyle() -> some View {
    self
        .background(.regularMaterial)
        .cornerRadius(16, style: .continuous)
        .shadow(color: .black.opacity(0.15), 
                radius: 20, y: 8)
}
```

## Animation System

### Spring Animations (Consistent)

```swift
// Panel toggles (Inspector/Formatter)
.spring(response: 0.35, dampingFraction: 0.85)

// Mode switching (Writer/Reader)
.spring(response: 0.3, dampingFraction: 0.85)

// Save button feedback
.scaleEffect(saving ? 0.97 : 1.0)
.animation(.easeOut(duration: 0.08))
```

## Spacing System

### Updated Layout

```swift
// Panel Configuration
Inspector Width: 320pt (macOS), 280pt (iOS)
Formatter Width: 320pt (macOS), 280pt (iOS)
Panel Spacing: 16pt between panels
Content Padding: 20pt

// Toolbar/Statusbar
Toolbar Height: 52pt
Statusbar Height: 30pt
Overlay Margins: 8pt
```

## Material Hierarchy

### Z-Layers (Updated)

```
1. Canvas background (AppBackground)
2. Paper document (Writer mode only)
3. Side panels (Inspector/Formatter)
4. Toolbar (top overlay)
5. Statusbar (bottom overlay)
6. Sheets and dialogs
7. Context menus
```

### Material Usage

```swift
.ultraThinMaterial:
  - ProjectToolbar
  - ProjectStatusbar
  - Buttons

.regularMaterial:
  - Inspector panel
  - Formatter panel
  - Dialogs

Paper (custom):
  - WriterWindow document area
  - Light appearance forced
```

## Icon System Updates

### Navigation Icons

```swift
// Simplified to 2 modes
Writer: "doc" (document icon)
Reader: "book" (book icon)

// Panel Controls
Inspector: "sidebar.left" / "sidebar.right"
Formatter: "sidebar.left" / "sidebar.right"

// Save States
Clean: "opticaldiscdrive"
Dirty: "opticaldiscdrive.fill"
Saving: Animation on icon
```

## Platform Adaptations

### macOS Specific

```swift
- Native save panel (NSSavePanel)
- "Open in Finder" in file browser
- Keyboard shortcut badges
- Hover states on buttons
- 320pt panel widths
- Cmd+S save shortcut
```

### iOS/iPadOS Specific

```swift
- Document directory saving
- Touch-optimized controls (44pt min)
- Glass button style
- 280pt panel widths
- No hover states
- Gesture-based interactions
```

## Visual State Indicators

### Save States

```swift
// Save Button Visual Feedback
Unsaved: Filled icon + accent color
Saved: Outline icon + secondary color
Saving: Pulsing animation

// Statusbar Save Display
"Saving..." - When actively saving
"Saved just now" - < 1 minute
"Saved X min ago" - Relative time
```

### Mode Indicators

```swift
// Toolbar Center Buttons
Selected: .blue foreground
Unselected: .secondary foreground
Transition: Spring animation
```

## Removed Visual Elements

### Deprecated Components
- ~~WriterToolbar design~~ (merged into ProjectToolbar)
- ~~Node-based mapper visuals~~ (moved to StoryMapper)
- ~~4-panel navigation~~ (simplified to 2 modes)
- ~~Multiple file browser designs~~ (unified)

## Quality Standards

### Current Achievements ✅
- Consistent paper document metaphor
- Unified toolbar design
- Complete save state visuals
- Smooth panel animations
- Clear visual hierarchy
- Platform-appropriate styling

### Areas for Enhancement 🎯
- MarkdownUI theme integration
- Loading state animations
- Export progress indicators
- Undo/redo visual feedback
- Find/replace overlay design

## Performance Considerations

### Visual Optimizations
- Paper shadow cached
- Minimal material layers
- Efficient state updates
- GPU-accelerated animations
- Lazy panel rendering

### Metrics
- 60fps animations maintained
- < 16ms render time
- Smooth panel transitions
- No shadow flickering
- Fast text rendering

## Accessibility

### Implemented
- Adjustable font sizes (11-28pt range)
- High contrast borders (0.08 opacity)
- Semantic color usage
- Platform color adaptation
- Sufficient touch targets

### Planned
- Dynamic Type support
- Reduced motion mode
- High contrast theme
- Enhanced focus indicators
- VoiceOver optimization

## Design Token Updates

### Reusable Styles
```swift
GlassButtonStyle()        // All buttons
floatingPanelStyle()      // Side panels
paperDocumentStyle()      // Writer document
ToolbarStyle.platform     // Platform variants
```

### Consistent Patterns
- Paper metaphor for content
- Glass morphism for UI
- Spring animations throughout
- Continuous corner radii
- State-based visual feedback

## Success Metrics

### Visual System (85% Complete)
- ✅ Paper document metaphor
- ✅ Unified toolbar design
- ✅ Statusbar implementation
- ✅ Panel animations
- ✅ Save state indicators
- ✅ Mode switching visuals
- ❌ MarkdownUI styling
- ❌ Export UI design

The visual system successfully creates a clear hierarchy between writing content (paper) and interface controls (glass), with smooth animations and consistent styling throughout.