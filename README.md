# StoryWriter Documentation

**Version**: 2.0.0  
**Last Updated**: January 2025  
**Platform**: macOS, iOS, tvOS (SwiftUI)  
**Architecture**: Behavior-Contract Component System

## Project Overview

StoryWriter is a comprehensive creative writing application that combines advanced markdown editing with visual story mapping. The application provides writers with both traditional text editing tools and innovative visual planning features through a node-based canvas system.

## Architecture Philosophy

### Behavior-Contract Pattern

The entire application is built on a **behavior-contract pattern** that ensures:

1. **Complete State Isolation**: UI components own no state
2. **Behavior Contracts**: Components receive configuration structs containing only closures
3. **Testability**: Every component can be tested in isolation
4. **Reusability**: Components work across macOS, iOS, and tvOS with platform-specific styling

### Key Principles

- **No Direct State Dependencies**: Views never access @StateObject or @ObservedObject directly
- **Configuration-Driven UI**: All behavior is injected through config structs
- **Platform Adaptive**: Single codebase with platform-specific enhancements
- **Glass-Morphism Design**: Consistent modern aesthetic across all components

## Core Systems

### 1. Toolbar System
A unified toolbar architecture used throughout the application:
- **MapperToolbar**: Node creation, zoom controls, grid/snap toggles
- **ProjectToolbar**: Section navigation, inspector toggle, project actions
- **WriterToolbar**: Font controls, word count, text operations

[→ Detailed Toolbar Documentation](./docs/ToolbarSystem.md)

### 2. Node System
Visual story mapping with rich node types:
- **19 Node Types** across 4 categories (Story, World, Gameplay, System)
- **Node States**: Normal, Placement, Editing, Connecting modes
- **Action Overlays**: Context-sensitive node actions
- **Smart Rendering**: Platform-optimized with hover states on macOS

[→ Detailed Node Documentation](./docs/NodeSystem.md)

### 3. Canvas System
Infinite canvas for visual story planning:
- **Gesture Handling**: Pan, zoom, drag with platform-specific implementations
- **Grid & Snapping**: Optional grid display with snap-to-grid
- **Edge Rendering**: Visual connections between nodes
- **Coordinate System**: Robust screen-to-canvas transformation

[→ Detailed Canvas Documentation](./docs/CanvasSystem.md)

### 4. MarkdownUI Engine
Custom markdown rendering system:
- **Complete DSL**: All standard markdown blocks and inlines
- **Theme System**: Customizable text and block styles
- **Syntax Highlighting**: Code block highlighting support
- **Table Support**: Full table rendering with alignment

[→ Detailed MarkdownUI Documentation](./docs/MarkdownUI.md)

## Project Structure

```
StoryWriter/
├── App/                    # Application entry and configuration
├── Core/                   # File system and project management
├── Inspector/              # Inspector panels and outliner
├── Managers/               # Project and state management
├── MarkdownUI/             # Custom markdown engine
│   ├── DSL/               # Domain-specific language
│   ├── Parser/            # Markdown parsing
│   ├── Renderer/          # Text rendering
│   ├── Theme/             # Styling system
│   └── Views/             # Markdown view components
├── Views/
│   ├── Mapper/            # Node-based story mapping
│   ├── Writer/            # Markdown editor
│   ├── Project/           # Project browser
│   ├── Character/         # Character management
│   ├── Index/             # Index card system
│   └── Timeline/          # Timeline features (planned)
└── Sources/
    └── Shared/            # Cross-platform utilities

```

## Component Status

| Component | Implementation | Testing | Documentation |
|-----------|---------------|---------|---------------|
| Toolbar System | ✅ Complete | 🔄 In Progress | ✅ Complete |
| Node System | 🔄 80% Complete | ⏳ Planned | ✅ Complete |
| Canvas System | ✅ Complete | 🔄 In Progress | 🔄 In Progress |
| MarkdownUI | ✅ Complete | ⏳ Planned | 🔄 In Progress |
| File Browser | ✅ Complete | ⏳ Planned | ⏳ Planned |
| Inspector | 🔄 In Progress | ⏳ Planned | ⏳ Planned |
| Project Manager | 🔄 In Progress | ⏳ Planned | ⏳ Planned |

## Development Patterns

### Config Pattern Example
```swift
// No state in the view
struct MapperToolbar: View {
    let config: MapperToolbarConfig  // Only behaviors
    let nodePalette: NodePalette     // Data provider
    
    var body: some View {
        Button(action: config.zoomIn) {
            // UI implementation
        }
    }
}

// Config contains only closures
struct MapperToolbarConfig {
    var zoomIn: () -> Void
    var zoomOut: () -> Void
    // ... other behaviors
}

// Parent owns state and passes behaviors
struct MapperWindow: View {
    @StateObject private var state = MapperState()
    
    private var toolbarConfig: MapperToolbarConfig {
        MapperToolbarConfig(
            zoomIn: { state.scale *= 1.15 },
            zoomOut: { state.scale *= 0.85 }
        )
    }
}
```

### Platform Adaptation
```swift
#if os(macOS)
    .toolbarStyle(.macOS)
    .onHover { hovering = $0 }  // macOS-only hover
#elseif os(iOS)
    .toolbarStyle(.iOS)
#endif
```

## Quick Start

### Running the Project
1. Open `StoryWriter.xcodeproj` in Xcode
2. Select target platform (macOS/iOS/tvOS)
3. Build and run (⌘R)

### Adding a New Node Type
1. Add case to `NodeTypes` enum
2. Implement protocol requirements (icon, color, category)
3. Node automatically appears in toolbar menu

### Creating a New Toolbar
1. Create config struct with behavior closures
2. Create toolbar view accepting config
3. Wire up in parent view with state

## Known Issues & TODOs

### High Priority
- [ ] Complete node connection system
- [ ] Implement node persistence
- [ ] Add undo/redo support
- [ ] Complete Inspector implementation

### Medium Priority
- [ ] Add Timeline view
- [ ] Implement project templates
- [ ] Add export functionality
- [ ] Create onboarding flow

### Low Priority
- [ ] Add keyboard shortcuts
- [ ] Implement themes
- [ ] Add collaboration features
- [ ] Create plugin system

## Contributing Guidelines

### Code Style
- Use behavior-contract pattern for all new components
- Maintain platform compatibility
- Include preview providers for all views
- Document complex logic with inline comments

### Testing
- Unit test config structs
- UI test critical user flows
- Snapshot test visual components
- Performance test canvas with 100+ nodes

## License

[Your License Here]

## Contact

Tyler Allen - [Contact Information]

---

For detailed documentation on specific systems, see the `/docs` directory.