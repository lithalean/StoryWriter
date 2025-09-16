# StoryWriter Architecture

**Version**: 3.0.0  
**Last Updated**: September 2025  
**Status**: Production Architecture  
**Completeness**: Architecture 95%, Implementation 60-65%

## System Overview

StoryWriter is a SwiftUI-based creative writing application implementing a **behavior-contract architecture** with a single-window, multi-panel design. The application supports macOS, iOS, and tvOS platforms, focusing on markdown-based writing with integrated story management tools.

```
┌─────────────────────────────────────────┐
│           StoryWriterApp                │
│         (SwiftData Container)           │
└────────────────┬────────────────────────┘
                 │
         ┌───────▼────────┐
         │  ContentView   │
         │ (Panel Router) │
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

### 2. Single-Window Architecture (Simplified)

- **ContentView Router**: Switches between Writer/Reader modes
- **ProjectToolbar**: Unified navigation and save controls
- **Inspector Sidebar**: File browser (left side)
- **Formatter Panel**: Text formatting controls (right side)
- **ProjectStatusbar**: Information display (bottom overlay)

### 3. State Management Architecture

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

// View State (Separate from Document)
@MainActor
final class WriterState: ObservableObject {
    @Published var fontSize: CGFloat = 15
    @Published var showLineNumbers: Bool = false
    @Published var wordWrap: Bool = true
    
    weak var document: WriterDocument?
    
    let minFontSize: CGFloat = 11
    let maxFontSize: CGFloat = 24
}

// Reader State (Foundation)
@MainActor
final class ReaderState: ObservableObject {
    @Published var markdown: String = ""
    @Published var fontSize: CGFloat = 18  // Larger for reading
    @Published var showStats: Bool = false
    
    let minFontSize: CGFloat = 12
    let maxFontSize: CGFloat = 28
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
    // No mapper data (separated to StoryMapper app)
}

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

## Module Organization (September 2025)

### Application Layer
```
App/
├── StoryWriterApp.swift       # Entry point
├── ContentView.swift          # Panel router & statusbar
├── AppBackground.swift        # Canvas texture
└── Item.swift                 # SwiftData model (future)
```

### Core Components
```
Writer/                        # ✅ Complete
├── WriterWindow.swift         # Paper document design
├── WriterDocument.swift       # Save/load functionality
└── WriterState.swift          # Editor preferences

Reader/                        # 🎯 Foundation ready
├── ReaderWindow.swift         # Book-style view
└── ReaderState.swift          # Reading preferences

Project/                       # ✅ Complete
├── ProjectToolbar.swift       # Unified toolbar
├── ProjectToolbarConfig.swift # Behavior contract
├── ProjectStatusbar.swift     # Bottom info display
├── ProjectStatusbarConfig.swift # Statusbar contract
└── ProjectState.swift         # Writer/Reader enum

Formatter/                     # ✅ Complete
├── FormatterPanel.swift       # Right sidebar
└── FormatControlsView.swift   # Text controls

Inspector/                     # ✅ Complete
├── InspectorPanel.swift       # Left sidebar
└── InspectorView.swift        # File browser
```

### UI-Only Components (No Backend)
```
Character/
└── CharacterSheet.swift       # Character UI (no persistence)

Index/
├── IndexWindow.swift          # Card grid (shelved)
└── IndexCard.swift            # Card component

Timeline/                      # Empty directory
```

### Core Systems
```
Core/
├── FileBrowser.swift          # ✅ Unified file browser
├── MarkdownReader.swift       # Placeholder for MarkdownUI
└── MarkdownWriter.swift       # TextEditor (temporary)

Managers/
└── ProjectManager.swift       # Project state & persistence

Sources/Shared/
├── GlassButtonStyle.swift     # Unified button style
├── ToolbarStyle.swift         # Platform adaptations
└── PlatformColor.swift        # Cross-platform colors
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
ProjectStatusbar updates (via config)
```

### Mode Switching

```swift
ContentView:
  currentSection → ProjectState enum
    ↓
  Switch statement → Show mode
    .writer: WriterWindow
    .reader: ReaderWindow
    ↓
  Mode loads with isolated state
```

### Panel Management

```
Left Panel (Inspector):
  - File browser
  - Toggle via toolbar
  
Center Content:
  - Writer or Reader mode
  - Always visible
  
Right Panel (Formatter):
  - Text formatting controls
  - Toggle via toolbar
  
Bottom Overlay (Statusbar):
  - Word count, save status
  - Always visible
```

## Design System

### Visual Hierarchy

1. **Background**: Canvas texture (AppBackground)
2. **Document**: Paper with shadow (Writer mode)
3. **Overlays**: Toolbar and statusbar
4. **Panels**: Glass morphism sidebars

### Glass-Morphism Theme

```swift
// Consistent styling across all components
.background(.ultraThinMaterial)
.cornerRadius(14, style: .continuous)
.shadow(radius: 10, y: 4)
.overlay(
    RoundedRectangle(cornerRadius: 14)
        .strokeBorder(Color.primary.opacity(0.08))
)
```

### Typography

```swift
// Writing (monospaced for editing)
Writer: .system(size: 15, design: .monospaced)

// Reading (serif for comfort)
Reader: .system(size: 18, design: .serif)

// UI (system default)
Interface: .system(size: 13)
```

## Platform Architecture

### Adaptive Design

```swift
#if os(macOS)
    // Native save panels
    NSSavePanel()
    // Keyboard shortcuts
    .keyboardShortcut("s", modifiers: [.command])
#elseif os(iOS)
    // Document directory saving
    // Touch-optimized targets
#elseif os(tvOS)
    // Focus engine support
    // Remote control optimized
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
- **Text Editing**: Native TextEditor (fast)
- **Save System**: Native dialogs (responsive)
- **Auto-save**: Timer-based (2 second delay)
- **Panel Switching**: < 50ms with animations
- **Mode Switching**: Instant with spring animation

### Optimization Targets
- Instant text response (<16ms)
- Markdown render <200ms for 10k words (when integrated)
- Auto-save without UI blocking
- Smooth 60fps animations

## Architectural Decisions

### Why Unified Toolbar?
- **Simplicity**: One toolbar for all controls
- **Clarity**: Navigation vs formatting separation
- **Save Integration**: Natural placement on right
- **Reduced Complexity**: Eliminated WriterToolbar

### Why Separate Writer/Reader Modes?
- **Focus**: Distinct editing vs reading experiences
- **Typography**: Different fonts for each purpose
- **Future**: Foundation for advanced reader features
- **Simplicity**: Just two modes instead of many panels

### Why Paper Document Design?
- **Visual Metaphor**: Familiar writing surface
- **Focus**: Document stands out from UI
- **Contrast**: Light paper on dark canvas
- **Professional**: Publication-ready appearance

### Why Behavior-Contract Throughout?
- **Testability**: Pure functions everywhere
- **Reusability**: Components work on all platforms
- **Clarity**: No hidden state dependencies
- **Maintainability**: Easy to understand and modify

## Quality Attributes

### Strengths ✅
- **Save System**: Complete with auto-save
- **Architecture**: Clean behavior-contract pattern
- **Toolbar**: Unified and simplified
- **Statusbar**: Full information display
- **File Browser**: Single implementation
- **Platform Support**: True multi-platform
- **Visual Design**: Cohesive glass morphism

### Current Limitations ❌
- **No Markdown Rendering**: TextEditor only
- **No Undo/Redo**: Critical missing feature
- **Character/Index**: UI only, no persistence
- **No Export**: Can't export to PDF/HTML
- **No Templates**: New documents start blank

### Technical Debt 🔧
- **MarkdownUI Integration**: 97 files ready but not connected
- **Timeline View**: Empty directory
- **Outline Panel**: Placeholder only
- **Section Title**: Hardcoded in statusbar

## Extension Points

### Adding Export Formats
1. Add export menu to toolbar
2. Create export service
3. Implement format converters
4. Add progress indicator

### Integrating MarkdownUI
1. Replace TextEditor in MarkdownWriter
2. Configure theme/styling
3. Add to ReaderWindow
4. Enable syntax highlighting

### Adding Persistence
1. Create CharacterModel
2. Add to WriterDocument
3. Include in save/load
4. Update UI bindings

## Testing Strategy

### Unit Tests
- Behavior contracts (pure functions)
- Document save/load cycles
- Font size boundaries
- Mode switching logic

### Integration Tests
- Toolbar interactions
- Panel synchronization
- Save system flow
- Platform differences

### UI Tests
- Animation smoothness
- Touch/click targets
- Accessibility compliance
- Keyboard shortcuts

## Future Architecture

### Immediate Priorities
- [x] Save functionality ✅
- [x] Unified toolbar ✅
- [x] Reader mode foundation ✅
- [ ] MarkdownUI integration
- [ ] Undo/redo system
- [ ] Character persistence

### Planned Improvements
- [ ] Export formats (PDF, DOCX, HTML)
- [ ] Document templates
- [ ] Theme customization
- [ ] Cloud sync
- [ ] Plugin system

### Strategic Considerations
- MarkdownUI as XCFramework for reuse
- Shared components with SyntaxWriter/LocalLLM
- Progressive disclosure of features
- Accessibility-first design

## Success Metrics

### Architecture (95%)
- ✅ Behavior-contract pattern complete
- ✅ Component isolation achieved
- ✅ Platform support working
- ✅ Save system integrated
- ❌ Some redundant code remains

### Implementation (60-65%)
- ✅ Core editing functional
- ✅ Save/load complete
- ✅ Navigation working
- ❌ MarkdownUI not integrated
- ❌ No undo/redo
- ❌ Character/Index not persistent

The architecture represents a mature, well-structured foundation ready for the remaining implementation work, particularly MarkdownUI integration which will transform the user experience.