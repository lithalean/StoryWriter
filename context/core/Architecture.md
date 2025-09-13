# StoryWriter Architecture

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
    ┌────────────┼────────────┬──────────┬──────────┐
    │            │            │          │          │
┌───▼────┐  ┌───▼────┐  ┌────▼───┐  ┌──▼───┐  ┌───▼────┐
│Inspector│  │ Writer │  │Character│  │Index │  │Timeline│
│  Panel  │  │ Window │  │  Sheet  │  │Window│  │(Future)│
└─────────┘  └────────┘  └─────────┘  └──────┘  └────────┘
```

## Core Architecture Patterns

### 1. Behavior-Contract Pattern

The entire application follows a strict behavior-contract pattern where:

```swift
// Components own no state
struct WriterToolbar: View {
    let config: WriterToolbarConfig  // Only behaviors
}

// Configs contain only closures and computed values
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
    // No @State, no stored properties
}

// Parents own state and pass behaviors
struct WriterWindow: View {
    @StateObject private var state = WriterState()
    
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
            isDirty: state.document?.isDirty ?? false
        )
    }
}
```

**Benefits:**
- Complete testability
- State isolation
- Platform reusability
- Clear data flow

### 2. Single-Window Panel Architecture

- **ContentView Router**: Switches between panels based on selection
- **ProjectToolbar**: Unified navigation across all views (3 main panels)
- **Inspector Sidebar**: Toggleable file browser/outliner
- **Floating Panel Style**: Consistent glass-morphism design

### 3. State Management Architecture

```swift
// Writer View State
@MainActor
final class WriterState: ObservableObject {
    @Published var fontSize: CGFloat = 14.0
    @Published var showLineNumbers: Bool = true
    @Published var wordWrap: Bool = true  // Future
    
    weak var document: WriterDocument?
    
    var wordCount: Int { /* computed from document */ }
    var lineCount: Int { /* computed from document */ }
    var characterCount: Int { /* computed from document */ }
    
    func incrementFontSize() {
        fontSize = min(24, fontSize + 1)
    }
    
    func decrementFontSize() {
        fontSize = max(11, fontSize - 1)
    }
}

// Document Model
@MainActor
final class WriterDocument: ObservableObject {
    @Published var content: String = ""
    @Published var title: String = "Untitled"
    @Published var isDirty: Bool = false
    
    private var saveTimer: Timer?
    private let autoSaveDelay: TimeInterval = 2.0
    
    func scheduleAutoSave() {
        saveTimer?.invalidate()
        saveTimer = Timer.scheduledTimer(withTimeInterval: autoSaveDelay, repeats: false) { _ in
            Task { @MainActor in
                self.save()
            }
        }
    }
}

// Project Management
@MainActor
final class ProjectManager: ObservableObject {
    @Published var currentProject: StoryProject?
    @Published var recentProjects: [StoryProjectFile] = []
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
    var characters: [StoryCharacter]  // Future
    var indexCards: [IndexCard]       // Future
    var tags: [String]
    var notes: String
    // No node/edge data (moved to StoryMapper)
}

// SwiftData (Scaffolded, unused)
@Model class Item {
    // Ready for future migration
}
```

### File Management

- **Extension**: `.storyproject`
- **Location**: `~/Documents/StoryWriter Projects` (macOS)
- **Format**: Pretty-printed JSON
- **Auto-save**: 2-second delay after changes
- **Recent Files**: UserDefaults with 10-item limit

## Module Organization

### Application Layer
```
App/
├── StoryWriterApp.swift       # Entry point
├── ContentView.swift          # Panel router
└── Item.swift                 # SwiftData model (future)
```

### View Components
```
Views/
├── Writer/
│   ├── WriterWindow.swift           # Container
│   ├── WriterDocument.swift         # Document model
│   ├── WriterState.swift            # View state
│   ├── WriterToolbar.swift          # Behavior-contract toolbar
│   ├── WriterToolbarConfig.swift    # Behaviors only
│   ├── WriterStatusbar.swift        # Bottom status
│   └── MarkdownWriter.swift         # Editor (TextEditor now, MarkdownUI future)
├── Character/
│   └── CharacterSheet.swift         # Character management (UI only)
├── Index/
│   ├── IndexWindow.swift            # Card grid
│   └── IndexCard.swift              # Card component
├── Timeline/                        # Empty (future)
└── StatefulPreviewWrapper.swift     # Testing helper
```

### Project Components
```
Project/
├── ProjectBrowser.swift         # Project selection
├── ProjectState.swift           # Section enum (3 panels)
├── ProjectToolbar.swift         # Main navigation
└── ProjectToolbarConfig.swift   # Behaviors only
```

### Shared Components
```
Sources/Shared/
├── GlassButtonStyle.swift      # Unified button style
├── ToolbarStyle.swift          # Platform adaptations
├── PlatformColor.swift         # Cross-platform colors
└── Extensions/
    ├── FilePermissionsHelper.swift
    └── HapticFeedback.swift
```

### Core Systems
```
Core/
├── FileSystem.swift            # File operations
├── FileBrowser.swift           # File browser UI
└── FileSystemModel.swift       # File system abstraction

Managers/
└── ProjectManager.swift        # Project state & persistence

Inspector/
├── InspectorPanel.swift        # Side panel container
├── InspectorView.swift         # Inspector content
└── OutlinerView.swift          # Document outline
```

### MarkdownUI (Ready for Integration)
```
MarkdownUI/                     # 97 files
├── DSL/                        # Markdown DSL
├── Parser/                     # Markdown parsing
├── Renderer/                   # Text rendering
├── Theme/                      # Styling system
├── Utility/                    # Helper functions
└── Views/                      # UI components
```

## Platform Architecture

### Adaptive Design

```swift
// Platform-specific implementations
#if os(macOS)
    .toolbarStyle(.macOS)
    .onHover { hovering = $0 }
#elseif os(iOS)
    .toolbarStyle(.iOS)
    .buttonStyle(GlassButtonStyle())
#elseif os(tvOS)
    .toolbarStyle(.iOS)  // Reuse iOS style
    .frame(width: 1920, height: 1080)
#endif
```

### Platform Requirements
- **macOS**: 14.0+ (Sonoma)
- **iOS**: 17.0+
- **tvOS**: 17.0+
- **Swift**: 5.9+
- **Xcode**: 15.0+

## Component Communication

### Data Flow

```
User Action (typing, toolbar click)
    ↓
WriterState/WriterDocument Update
    ↓
Auto-save Timer (2 seconds)
    ↓
@Published property change
    ↓
SwiftUI Redraw
    ↓
Child Components Update
```

### Panel Switching

```swift
ContentView:
  selectedCenterButton → ProjectState enum
    ↓
  Switch statement → Show panel
    0: Writer
    1: Characters  
    2: Index
    ↓
  Panel loads with isolated state
```

## Design System

### Glass-Morphism Theme

```swift
// Consistent styling across all components
.background(.ultraThinMaterial)
.cornerRadius(14, style: .continuous)
.shadow(radius: 10, y: 4)
```

### Spacing Guidelines
- Panel spacing: 16pt (HIG standard)
- Container padding: 20pt
- Toolbar margins: 12pt horizontal, 12pt top
- Internal padding: 8-10pt

## Performance Characteristics

### Current Implementation
- **Text Editing**: Native TextEditor (fast)
- **Markdown Rendering**: Not integrated yet
- **File I/O**: Synchronous operations
- **State Updates**: @Published with Combine
- **Auto-save**: Timer-based (2 second delay)

### Optimization Targets
- Instant text response (<16ms)
- Sub-100ms panel switching
- Markdown render <200ms for 10k words
- Auto-save without UI blocking

## Architectural Decisions

### Why Behavior-Contract Pattern?
- **Testability**: Pure functions are easy to test
- **Reusability**: Components work on all platforms
- **Clarity**: Clear separation of concerns
- **Maintainability**: No hidden state dependencies

### Why Single Window?
- **Simplicity**: One window, multiple panels
- **iOS Compatibility**: Natural on mobile
- **State Management**: Centralized routing
- **User Experience**: Familiar panel switching

### Why Separate WriterState and WriterDocument?
- **Separation of Concerns**: View state vs document model
- **Persistence**: Document handles saving, state handles UI
- **Testing**: Can test document logic without UI
- **Reusability**: Document model can be used elsewhere

### Why JSON Storage?
- **Human Readable**: Debug and inspect files
- **Simple**: No migration complexity
- **Portable**: Easy backup/sharing
- **Future-Proof**: SwiftData ready when needed

## Quality Attributes

### Strengths
- **Clean Architecture**: Behavior-contract throughout
- **Platform Support**: True multi-platform
- **Extensibility**: Easy to add features
- **Consistency**: Unified design system
- **Testability**: Isolated components
- **Document Model**: Clear separation of concerns

### Current Limitations
- **No Markdown Rendering**: TextEditor only
- **No Undo/Redo**: Not implemented
- **Character/Index Persistence**: UI only, no data
- **Limited Accessibility**: VoiceOver incomplete
- **File Browser Redundancy**: 3 implementations

### Technical Debt
- **Three File Browsers**: Need consolidation
- **MarkdownUI Integration**: 97 files ready but not connected
- **Timeline View**: Directory exists, not implemented
- **Outline Panel**: Placeholder only
- **Location Directory**: Should be removed (oversight)

## Extension Points

### Adding Panels
1. Create Window view
2. Add to ContentView switch
3. Add to ProjectState enum
4. Update toolbar config

### Integrating MarkdownUI
1. Replace TextEditor in MarkdownWriter
2. Configure theme/styling
3. Add syntax highlighting
4. Connect to WriterDocument

### Adding Platforms
- watchOS: Possible with reduced features
- visionOS: Natural fit for spatial computing

## Testing Strategy

### Unit Tests
- WriterState operations (font size bounds)
- WriterDocument save/load
- Config structs (pure functions)
- File I/O operations

### UI Tests
- Panel switching
- Toolbar interactions
- Text editing flow
- Auto-save behavior

### Integration Tests
- Project save/load
- Multi-platform behavior
- MarkdownUI rendering (future)

## Future Architecture

### Immediate Priorities
- [x] WriterDocument implementation
- [x] WriterState management
- [ ] MarkdownUI integration
- [ ] File browser consolidation
- [ ] Character data persistence

### Planned Improvements
- [ ] SwiftData migration
- [ ] Undo/redo system
- [ ] Export formats (PDF, DOCX, HTML)
- [ ] Cloud sync
- [ ] Collaborative editing

### Strategic Considerations
- MarkdownUI as XCFramework for reuse
- Shared components with SyntaxWriter/LocalLLM
- Plugin system for export formats
- Theme customization system