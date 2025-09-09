# StoryWriter Architecture

## System Overview

StoryWriter is a SwiftUI-based creative writing application implementing a **behavior-contract architecture** with a single-window, multi-panel design. The application supports macOS, iOS, and tvOS platforms.

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
│Inspector│  │ Writer │  │ Mapper │  │Index │  │Outline │
│  Panel  │  │ Window │  │ Window │  │Window│  │(Future)│
└─────────┘  └────────┘  └────────┘  └──────┘  └────────┘
```

## Core Architecture Patterns

### 1. Behavior-Contract Pattern

The entire application follows a strict behavior-contract pattern where:

```swift
// Components own no state
struct MapperToolbar: View {
    let config: MapperToolbarConfig  // Only behaviors
    let nodePalette: NodePalette     // Data provider
}

// Configs contain only closures
struct MapperToolbarConfig {
    var zoomIn: () -> Void
    var zoomOut: () -> Void
    // No @State, no stored properties
}

// Parents own state and pass behaviors
struct MapperWindow: View {
    @StateObject private var state = MapperState()
    
    private var toolbarConfig: MapperToolbarConfig {
        MapperToolbarConfig(
            zoomIn: { state.scale *= 1.15 }
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
- **ProjectToolbar**: Unified navigation across all views
- **Inspector Sidebar**: Toggleable file browser/outliner
- **Floating Panel Style**: Consistent glass-morphism design

### 3. Store-Based State Management

```swift
// Canvas state
@MainActor
final class MapperState: ObservableObject {
    @Published var scale: CGFloat = 1.0
    @Published var offset: CGSize = .zero
    @Published var showGrid: Bool = true
    @Published var snapToGrid: Bool = true
}

// Node management
@MainActor
final class NodeStore: ObservableObject {
    @Published var nodes: [StoryNode] = []
    @Published var selectedNodeID: UUID?
}

// Project management
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
    var tags: [String]
    var notes: String
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
│   ├── WriterToolbar.swift          # Behavior-contract toolbar
│   ├── WriterToolbarConfig.swift    # Behaviors only
│   └── StatefulPreviewWrapper.swift # Testing helper
├── Mapper/
│   ├── MapperWindow.swift           # Canvas container
│   ├── MapperToolbar.swift          # Behavior-contract toolbar
│   ├── MapperToolbarConfig.swift    # Behaviors only
│   ├── NodeUI.swift                 # Composable node view
│   ├── NodeState.swift              # State machine
│   ├── NodeActionOverlay.swift      # Context actions
│   └── NodeTypes.swift              # 19 type definitions
├── Project/
│   ├── ProjectToolbar.swift         # Main navigation
│   ├── ProjectToolbarConfig.swift   # Behaviors only
│   └── ProjectState.swift           # Section enum
└── Index/
    └── IndexWindow.swift             # Card grid
```

### Shared Components
```
Sources/Shared/
├── GlassButtonStyle.swift      # Unified button style
├── ToolbarStyle.swift          # Platform adaptations
└── PlatformColor.swift         # Cross-platform colors
```

### Core Systems
```
Core/
├── FileSystem.swift            # File operations
└── FileBrowser.swift           # File browser UI

Managers/
└── ProjectManager.swift        # Project state & persistence
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
User Action
    ↓
Toolbar Config Closure
    ↓
Parent State Update (@Published)
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
- **Rendering**: Direct SwiftUI, no virtualization
- **Canvas**: All nodes rendered (no culling)
- **File I/O**: Synchronous operations
- **State Updates**: @Published with Combine

### Optimization Targets
- 60fps with 500 nodes
- Sub-100ms panel switching
- Instant toolbar response

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

### Why JSON Storage?
- **Human Readable**: Debug and inspect files
- **Simple**: No migration complexity
- **Portable**: Easy backup/sharing
- **Future-Proof**: SwiftData ready when needed

## Quality Attributes

### Strengths
- **Clean Architecture**: Behavior-contract throughout
- **Platform Support**: True multi-platform
- **Extensibility**: Easy to add node types
- **Consistency**: Unified design system
- **Testability**: Isolated components

### Current Limitations
- **No Persistence**: Nodes not saved
- **No Undo/Redo**: Not implemented
- **No Virtualization**: Performance limit ~500 nodes
- **Limited Accessibility**: VoiceOver incomplete

### Technical Debt
- **WriterDocument**: Referenced but not implemented
- **Outline Panel**: Placeholder only
- **Connection System**: 70% complete
- **Timeline View**: Directory exists, not implemented

## Extension Points

### Adding Node Types
```swift
// Simply extend the enum
enum NodeTypes {
    case newType = "newType"
}
```

### Adding Panels
1. Create Window view
2. Add to ContentView switch
3. Add to ProjectState enum
4. Update toolbar config

### Adding Platforms
- watchOS: Possible with reduced features
- visionOS: Natural fit for spatial computing

## Testing Strategy

### Unit Tests
- Config structs (pure functions)
- Store operations
- File I/O

### UI Tests
- Panel switching
- Toolbar interactions
- Node placement flow

### Integration Tests
- Project save/load
- Multi-platform behavior
- Performance benchmarks

## Future Architecture

### Planned Improvements
- [ ] SwiftData migration
- [ ] Plugin system for node types
- [ ] Collaborative editing
- [ ] Cloud sync
- [ ] Custom markdown renderer integration

### Considered Patterns
- Coordinator pattern for navigation
- Repository pattern for data
- Command pattern for undo/redo
- Strategy pattern for export formats