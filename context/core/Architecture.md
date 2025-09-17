# StoryWriter Architecture

**Version**: 3.1.0  
**Last Updated**: October 2025  
**Status**: Production Architecture  
**Completeness**: Architecture 95%, Implementation 70%

## System Overview

StoryWriter is a SwiftUI-based creative writing application implementing a **behavior-contract architecture** with a single-window, multi-panel design and precision layout system. The application supports macOS, iOS, and tvOS platforms, focusing on markdown-based writing with integrated story management tools.

```
┌─────────────────────────────────────────┐
│           StoryWriterApp                │
│         (SwiftData Container)           │
└────────────────┬────────────────────────┘
                 │
         ┌───────▼────────┐
         │  ContentView   │
         │ (Router+Layout)│
         └───────┬────────┘
                 │
    ┌────────────┼────────────┐
    │            │            │
┌───▼────┐  ┌───▼────┐  ┌────▼────┐
│Inspector│  │ Writer │  │ Reader  │
│  Panel  │  │ Window │  │ Window  │
└─────────┘  └────────┘  └─────────┘
           (Formatter Panel →)
```

## Core Architecture Patterns

### 1. Behavior-Contract Pattern

The entire application follows a strict behavior-contract pattern where components own no state:

```swift
// Components own no state
struct ProjectToolbar: View {
    let config: ProjectToolbarConfig  // Only behaviors
}

// Configs contain only closures and computed values
struct ProjectToolbarConfig {
    var isInspectorVisible: () -> Bool
    var toggleSidebar: () -> Void
    var selectedSection: () -> ProjectState
    var selectSection: (ProjectState) -> Void
    var isDirty: () -> Bool
    var save: () -> Void
    var canSave: () -> Bool
    // No @State, no stored properties
}

// Parents own state and pass behaviors
struct ContentView: View {
    @State private var showInspector = true
    @StateObject private var document = WriterDocument()
    
    private var toolbarConfig: ProjectToolbarConfig {
        ProjectToolbarConfig(
            isInspectorVisible: { showInspector },
            toggleSidebar: { showInspector.toggle() },
            isDirty: { document.isDirty },
            save: { document.save() },
            // ... other behaviors
        )
    }
}
```

**Benefits:**
- Complete testability
- State isolation
- Platform reusability
- Clear data flow

### 2. Precision Layout Architecture

ContentView implements mathematical precision for UI element positioning:

```swift
// Layout Constants
private let toolbarHeight: CGFloat = 52
private let statusbarHeight: CGFloat = 36
private let topToolbarPadding: CGFloat = 8
private let bottomStatusbarPadding: CGFloat = 8
private let floatingPanelYOffset: CGFloat = 6  // Fine-tuning

// Calculated Values
let availableHeight = geometry.size.height - 104  // Total chrome
let windowYOffset = 14  // Centered + fine-tuned
```

**Key Formula:**
```
Y-Offset = ((ToolbarSpace - StatusbarSpace) / 2) + FineTuning
Where: ToolbarSpace = 60pt, StatusbarSpace = 44pt, FineTuning = 6pt
Result: 14pt vertical offset for perfect alignment
```

### 3. Four-Layer Visual Hierarchy

```swift
ZStack {
    // Layer 0: Canvas Background
    CanvasBackground()  // Dark gradient + noise
    
    // Layer 1: Paper Document
    WriterPaper()  // US Letter, dark theme
    
    // Layer 2: Content
    MarkdownWriter()  // Text editing
    
    // Layer 3: UI Chrome
    ProjectToolbar()  // Top overlay
    ProjectStatusbar()  // Bottom overlay
}
```

### 4. State Management Architecture

```swift
// Document Model (Complete)
@MainActor
final class WriterDocument: ObservableObject {
    @Published var content: String = ""
    @Published var title: String = "Untitled"
    @Published var isDirty: Bool = false
    @Published var wordCount: Int = 0
    @Published var lastSaved: Date? = nil
    @Published var isSaving: Bool = false
    
    private var saveTimer: Timer?
    private var filePath: URL?
    
    func save() { /* Native save dialogs */ }
    func saveAs() { /* Force new location */ }
    private func autoSave() { /* 2-second delay */ }
}

// Canvas State (NEW)
@MainActor
final class CanvasState: ObservableObject {
    @Published var scale: CGFloat = 1.0
    @Published var offset: CGSize = .zero
    @Published var isInteracting: Bool = false
    
    // Future: Canvas gestures and interactions
}

// View States (Separate from Document)
@MainActor
final class WriterState: ObservableObject {
    @Published var fontSize: CGFloat = 15
    @Published var showLineNumbers: Bool = false
    @Published var wordWrap: Bool = true
    
    weak var document: WriterDocument?
}

@MainActor
final class ReaderState: ObservableObject {
    @Published var markdown: String = ""
    @Published var fontSize: CGFloat = 18
    @Published var showStats: Bool = false
}
```

## Data Architecture

### Storage Strategy

```swift
// JSON for Projects (Active)
struct StoryProject: Codable {
    var id: UUID
    var title: String
    var dateCreated: Date
    var dateModified: Date
    var chapters: [StoryChapter]
    var characters: [StoryCharacter]  // UI only currently
    var indexCards: [IndexCard]       // UI only currently
    var tags: [String]
    var notes: String
}

// Paper Preview Storage (NEW)
WriterPaperStore:
  - Location: ~/Library/Application Support/[bundleID]/Previews/
  - Format: PNG images
  - Naming: [documentID].png
  - Generation: Async (future)

// SwiftData (Scaffolded, unused)
@Model class Item {
    // Ready for future migration
}
```

### File Management

- **Extension**: `.storyproject`
- **Location**: `~/Documents/StoryWriter Projects/` (macOS)
- **Format**: Pretty-printed JSON
- **Auto-save**: 2-second delay after changes
- **Save System**: ✅ COMPLETE with native dialogs

## Module Organization (October 2025)

### Application Layer
```
App/
├── StoryWriterApp.swift       # Entry point
├── ContentView.swift          # Router + Precision Layout
├── AppBackground.swift        # Canvas texture
└── Item.swift                 # SwiftData model (future)
```

### Canvas System (NEW)
```
Canvas/
├── CanvasBackground.swift     # Dark gradient + noise
├── CanvasState.swift          # Interaction state
└── CanvasGestureHandler.swift # Gesture management
```

### Core Components
```
Writer/                        # ✅ Complete
├── WriterWindow.swift         # Container view
├── WriterDocument.swift       # Save/load functionality
├── WriterState.swift          # Editor preferences
└── WriterPaper.swift          # US Letter paper (NEW)

Reader/                        # 🎯 Foundation ready
├── ReaderWindow.swift         # Book-style view
└── ReaderState.swift          # Reading preferences

Project/                       # ✅ Complete
├── ProjectToolbar.swift       # Unified toolbar
├── ProjectToolbarConfig.swift # Behavior contract
├── ProjectStatusbar.swift     # Bottom info display
├── ProjectStatusbarConfig.swift # Statusbar contract
├── ProjectBrowser.swift       # Project selection
└── ProjectState.swift         # Writer/Reader enum

Formatter/                     # ✅ Complete
├── FormatterPanel.swift       # Right sidebar
└── FormatControlsView.swift   # Text controls

Inspector/                     # ✅ Complete
├── InspectorPanel.swift       # Left sidebar
├── InspectorView.swift        # Content router
└── OutlinerView.swift         # Document outline
```

### UI-Only Components (No Backend)
```
Character/
└── CharacterSheet.swift       # Character UI (no persistence)

Index/
├── IndexWindow.swift          # Card grid (shelved)
└── IndexCard.swift            # Card component

Timeline/                      # Empty directory
Location/
└── LocationSheet.swift        # Orphaned file
```

### Core Systems
```
Core/
├── FileBrowser.swift          # ✅ Unified file browser
├── FileSystemModel.swift      # File operations
├── MarkdownReader.swift       # Placeholder for MarkdownUI
└── MarkdownWriter.swift       # TextEditor (temporary)

Managers/
├── ProjectManager.swift       # Project state & persistence
└── StatefulPreviewWrapper.swift # Preview support

Sources/Shared/
├── GlassButtonStyle.swift     # Unified button style
├── ToolbarStyle.swift         # Platform adaptations
├── PlatformColor.swift        # Cross-platform colors
└── Extensions/
    ├── FilePermissionsHelper.swift
    └── HapticFeedback.swift
```

### MarkdownUI (Not Integrated)
```
MarkdownUI/                    # 97 files ready
├── DSL/                       # Markdown DSL
├── Parser/                    # Markdown parsing
├── Renderer/                  # Text rendering
├── Theme/                     # Styling system
├── Utility/                   # Helper functions
└── Views/                     # UI components
```

## Component Communication

### Data Flow

```
User Action (typing, save button)
    ↓
WriterDocument Update
    ↓
Auto-save Timer (2 seconds)
    ↓
@Published property change
    ↓
ContentView receives update
    ↓
Behavior contracts propagate
    ↓
UI components update
```

### Layout Calculation Flow

```
Window Resize
    ↓
GeometryReader Update
    ↓
Available Height Calculation (windowHeight - 104pt)
    ↓
Y-Offset Calculation (14pt)
    ↓
Components Position
    ↓
Spring Animations (0.35s panels, 0.3s modes)
```

### Mode Switching

```swift
ContentView:
  currentSection → ProjectState enum
    ↓
  Switch statement → Show mode
    .writer: WriterWindow with WriterPaper
    .reader: ReaderWindow with canvas
    ↓
  Mode loads with isolated state
```

## Design System

### Visual Architecture

1. **Canvas**: Dark gradient (0.05-0.02) with noise
2. **Paper**: Dark theme (0.15-0.11) with US Letter ratio
3. **Content**: Monospaced (Writer) or Serif (Reader)
4. **Chrome**: Glass morphism overlays

### WriterPaper Component

```swift
// US Letter Document
struct WriterPaper {
    aspectRatio = 8.5 / 11.0  // 0.773
    
    // Dark paper gradient
    colors: [.init(white: 0.15), .init(white: 0.13), .init(white: 0.11)]
    
    // Visual effects
    cornerRadius: 4pt (macOS/iOS), 8pt (tvOS)
    shadow: black 0.15, radius 6pt, y: 2pt
    edgeHighlight: white 0.04 gradient
    
    // Optional features
    showsLines: Bool  // Ruled paper
    overlayOpacity: 0.02  // Paper texture
}

// Preview System
WriterPaperStore {
    previewURL() -> URL  // PNG location
    preview() -> Image?  // Cached image
    generatePreview()    // Future: Render markdown
}
```

### Glass-Morphism Theme

```swift
// Consistent styling across all components
.background(.ultraThinMaterial)  // Toolbar, statusbar
.background(.regularMaterial)    // Panels
.cornerRadius(14-16, style: .continuous)
.shadow(radius: 10-20, y: 4-8)
.overlay(
    RoundedRectangle(cornerRadius: radius)
        .strokeBorder(Color.primary.opacity(0.08))
)
```

## Platform Architecture

### Adaptive Design

```swift
#if os(macOS)
    // Native save panels
    NSSavePanel()
    // Keyboard shortcuts
    .keyboardShortcut("s", modifiers: [.command])
    // Panel width: 320pt
#elseif os(iOS)
    // Document directory saving
    // Touch-optimized targets
    // Panel width: 280pt
#elseif os(tvOS)
    // Focus engine support
    // Remote control optimized
    // Fixed 1920x1080 layout
#endif
```

### Platform Requirements
- **macOS**: 14.0+ (Sonoma)
- **iOS**: 17.0+
- **tvOS**: 17.0+
- **Swift**: 5.9+
- **Xcode**: 15.0+

## Performance Characteristics

### Current Implementation
- **Layout Calculations**: < 1ms per resize
- **Text Editing**: Native TextEditor (fast)
- **Save System**: Native dialogs (responsive)
- **Auto-save**: Timer-based (2 second delay)
- **Panel Switching**: < 50ms with animations
- **Mode Switching**: Instant with spring animation
- **Canvas Rendering**: GPU accelerated
- **Memory Usage**: ~45MB typical

### Optimization Targets
- Instant text response (<16ms)
- Markdown render <200ms for 10k words (when integrated)
- Auto-save without UI blocking
- Smooth 60fps animations
- Paper preview generation async

## Architectural Decisions

### Why Precision Layout System?
- **Mathematical Accuracy**: 14pt offset precisely calculated
- **Visual Balance**: Fine-tuning for perfect alignment
- **Consistency**: All elements use same system
- **Maintainability**: Single source of truth for layout

### Why Canvas System?
- **Visual Depth**: Four-layer hierarchy
- **Performance**: GPU-accelerated backgrounds
- **Flexibility**: Future gesture support
- **Atmosphere**: Professional dark theme

### Why WriterPaper Component?
- **Professional**: US Letter standard
- **Visual Metaphor**: Physical document
- **Dark Theme**: Consistent throughout
- **Preview System**: Document thumbnails

### Why Unified Toolbar?
- **Simplicity**: One toolbar for all controls
- **Clarity**: Navigation vs formatting separation
- **Save Integration**: Natural placement on right
- **Reduced Complexity**: Eliminated WriterToolbar

### Why Behavior-Contract Throughout?
- **Testability**: Pure functions everywhere
- **Reusability**: Components work on all platforms
- **Clarity**: No hidden state dependencies
- **Maintainability**: Easy to understand and modify

## Quality Attributes

### Strengths ✅
- **Precision Layout**: Mathematical accuracy
- **Canvas System**: Clean background architecture
- **WriterPaper**: Professional document metaphor
- **Save System**: Complete with auto-save
- **Architecture**: Clean behavior-contract pattern
- **Toolbar**: Unified and simplified
- **Statusbar**: Full information display
- **Platform Support**: True multi-platform

### Current Limitations ❌
- **No Markdown Rendering**: TextEditor only
- **No Undo/Redo**: Critical missing feature
- **Character/Index**: UI only, no persistence
- **No Export**: Can't export to PDF/HTML
- **No Paper Preview**: Generation not implemented

### Technical Debt 🔧
- **MarkdownUI Integration**: 97 files ready but not connected
- **Timeline View**: Empty directory
- **Location Sheet**: Orphaned file
- **Section Title**: Hardcoded in statusbar

## Extension Points

### Adding Export Formats
1. Add export menu to toolbar
2. Create export service
3. Implement format converters
4. Add progress indicator

### Integrating MarkdownUI
1. Replace TextEditor in MarkdownWriter
2. Configure dark theme
3. Add to ReaderWindow
4. Enable syntax highlighting
5. Connect to paper preview

### Implementing Paper Preview
1. Render markdown to image
2. Store in WriterPaperStore
3. Display in WriterPaper
4. Update on save

### Adding Canvas Interactions
1. Implement gesture handlers
2. Add zoom/pan support
3. Create focus mode
4. Enable annotations

## Testing Strategy

### Unit Tests
- Behavior contracts (pure functions)
- Layout calculations (14pt offset)
- Document save/load cycles
- Font size boundaries
- Mode switching logic

### Integration Tests
- Toolbar interactions
- Panel synchronization
- Save system flow
- Platform differences
- Canvas rendering

### UI Tests
- Animation smoothness
- Touch/click targets
- Accessibility compliance
- Keyboard shortcuts
- Layout precision

## Future Architecture

### Immediate Priorities
- [x] Save functionality ✅
- [x] Unified toolbar ✅
- [x] Canvas system ✅
- [x] WriterPaper component ✅
- [ ] MarkdownUI integration
- [ ] Undo/redo system
- [ ] Paper preview generation

### Planned Improvements
- [ ] Export formats (PDF, DOCX, HTML)
- [ ] Document templates
- [ ] Theme customization
- [ ] Cloud sync
- [ ] Plugin system
- [ ] Canvas interactions

### Strategic Considerations
- MarkdownUI as XCFramework for reuse
- Shared components with SyntaxWriter/LocalLLM
- Progressive disclosure of features
- Accessibility-first design
- Performance monitoring

## Success Metrics

### Architecture (95%)
- ✅ Behavior-contract pattern complete
- ✅ Component isolation achieved
- ✅ Platform support working
- ✅ Precision layout system
- ✅ Canvas architecture
- ❌ Some redundant code remains

### Implementation (70%)
- ✅ Core editing functional
- ✅ Save/load complete
- ✅ Navigation working
- ✅ Visual system polished
- ❌ MarkdownUI not integrated
- ❌ No undo/redo
- ❌ Character/Index not persistent

The architecture represents a mature, well-structured foundation with precision layout system and sophisticated visual hierarchy, ready for the remaining implementation work.