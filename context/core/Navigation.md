# StoryWriter Navigation & User Flows

**Architecture**: Single-window with panel switching  
**Pattern**: Behavior-contract navigation  
**Platform Support**: macOS, iOS, tvOS

## Navigation Architecture

### Single-Window Design

Despite component names ending in "Window", StoryWriter uses a **single-window architecture** with switchable panels:

```
StoryWriterApp
    ↓
ContentView (Router)
    ├── ProjectToolbar (Persistent navigation)
    ├── Inspector Panel (Toggleable sidebar)
    └── Main Panel (Switchable content)
        ├── WriterWindow
        ├── IndexWindow  
        ├── MapperWindow
        └── Outline (Placeholder)
```

## Navigation Control

### Primary Navigation - ProjectToolbar

Using behavior-contract pattern:

```swift
ProjectToolbarConfig:
  selectedSection: () -> ProjectState
  selectSection: (ProjectState) -> Void
```

**Sections:**
- `storyFiles` → WriterWindow
- `chapters` → Outline (placeholder)
- `characters` → IndexWindow
- `locations` → MapperWindow

### Inspector Toggle

```swift
ProjectToolbarConfig:
  isInspectorVisible: () -> Bool
  toggleSidebar: () -> Void
```

Animated with spring: `response: 0.35, dampingFraction: 0.85`

## User Flows

### 1. Writing Flow

```
User clicks story files icon
    → ContentView.selectedCenterButton = 0
    → WriterWindow displays
        → WriterToolbar (functional)
            - Font size slider (11-24pt)
            - Clear text button
            - Word count display
        → MarkdownWriter (TextEditor)
        → WriterStatusbar (document info)

Capabilities:
✅ Adjust font size
✅ Clear document
✅ See word count
✅ Type in monospace editor
⏳ Markdown rendering (MarkdownUI ready)
❌ Document persistence
```

### 2. Mapper Flow

```
User clicks locations icon
    → ContentView.selectedCenterButton = 3
    → MapperWindow displays
        → MapperToolbar
            - Zoom in/out
            - Reset view
            - Toggle grid/snap
            - Add node menu (19 types)
        → Canvas with nodes/edges

Capabilities:
✅ Add nodes (placement mode)
✅ Drag nodes around
✅ Pan/zoom canvas (gestures)
✅ Delete nodes (hover on macOS)
✅ Snap to grid (20pt)
🔄 Connect nodes (70% complete)
❌ Save map to project
```

### 3. Index Card Flow

```
User clicks characters icon
    → ContentView.selectedCenterButton = 2
    → IndexWindow displays
        → Responsive grid (220pt min width)
        → 9 static cards

Capabilities:
✅ Responsive layout
🎨 Visual design complete
❌ Card interaction
❌ Data binding
```

### 4. Inspector/File Browser Flow

```
User toggles sidebar
    → showInspector = true/false
    → InspectorPanel displays
        → Draggable divider
        → OutlinerView (top)
            → FileBrowser
        → PropertiesView (bottom, empty)

Capabilities:
✅ Browse file tree
✅ Create folders/markdown files  
✅ Delete items
✅ Expand/collapse folders
✅ Color-coded icons
✅ Hover states (macOS)
❌ Open files in editor
❌ Properties editing
```

## Gesture System

### Canvas Gestures (Mapper)

```swift
CanvasGestureHandler:
  - MagnificationGesture (pinch zoom)
  - DragGesture (pan)
  - Snap to common zoom levels
  - Coordinate conversion (screen ↔ canvas)
```

**Zoom Levels:** 0.25x, 0.5x, 0.75x, 1.0x, 1.5x, 2.0x, 3.0x  
**Grid Snap:** 20pt grid when enabled

### Inspector Gestures

```swift
DraggableDivider:
  - Vertical resize
  - Min section height: 100pt
  - Animated cursor change (macOS)
  - Visual feedback on drag
```

### Node Gestures

```swift
NodeRenderer:
  - Tap to select
  - Drag to move
  - Hover for delete (macOS)
  - Double-tap (defined, usage unclear)
```

## State Management

### Global State (ContentView)

```swift
@StateObject projectManager = ProjectManager()
@StateObject document = WriterDocument() // Referenced but doesn't exist
@State showInspector = true
@State selectedCenterButton = 0
```

### Panel-Specific State

**MapperWindow:**
```swift
@StateObject state = MapperState()      // Zoom, pan, grid
@StateObject nodeStore = NodeStore()    // Node management
@StateObject edgeStore = EdgeStore()    // Edge management
@State pendingAdd: NodeTypes?           // Placement mode
@State pendingNode: StoryNode?          // Node being placed
```

**WriterWindow:**
```swift
@State fontSize: CGFloat = 15
@State markdownText: String = ""
@StateObject document = WriterDocument() // Doesn't exist
@StateObject projectManager              // Duplicate of global
```

## Animation System

### Consistent Spring Animations

```swift
// Panel transitions
.spring(response: 0.35, dampingFraction: 0.85)

// Node interactions
.spring(response: 0.3, dampingFraction: 0.8)

// Quick feedback
.spring(response: 0.2, dampingFraction: 0.9)

// Divider dragging
.interactiveSpring(response: 0.15, dampingFraction: 1)
```

## Platform Adaptations

### macOS
- Hover states (nodes, file items)
- Cursor changes (resize, arrow)
- Native menu styling
- 320pt inspector width

### iOS/iPadOS
- Touch-optimized targets
- Glass button style for menus
- 280pt inspector width
- Sheet presentations

### tvOS
- Preview support
- Fixed 1920x1080 layout
- Simplified interactions
- iOS toolbar style

## Modal & Sheet System

### Implemented Sheets

**FileBrowserCreateSheet:**
```swift
- Create folder or markdown file
- Name input field
- Cancel/Create actions
- NavigationView wrapper
```

### Missing Modals
- Project creation
- Save/Open dialogs
- Export options
- Settings/Preferences
- Character/Location sheets (UI exists, not connected)

## Data Flow

### Behavior-Contract Navigation

All navigation uses closures, no direct state:

```swift
// Toolbar owns no state
ProjectToolbar(config: ProjectToolbarConfig)

// Parent provides behaviors
ContentView:
  config.selectSection = { section in
    switch section {
      case .storyFiles: selectedCenterButton = 0
      case .characters: selectedCenterButton = 2
      // etc.
    }
  }
```

### Panel Isolation

Each panel maintains independent state:
- No shared data between panels
- ProjectManager created but unused
- Local state not persisted

## Accessibility

### Implemented
- `.help()` tooltips on some buttons
- Semantic colors
- Standard SwiftUI accessibility
- Focus indicators (partial)

### Missing
- VoiceOver labels
- Keyboard navigation
- Tab order
- Skip links
- Role definitions

## Performance

### Current Metrics
- Panel switch: < 50ms
- Inspector toggle: Animated ~350ms
- Node drag: 60fps up to 100 nodes
- File browser: Instant for < 100 items

### Bottlenecks
- No virtualization (all nodes rendered)
- File browser reloads entire tree
- No lazy loading
- Synchronous file operations

## Working Features

| Feature | Status | Notes |
|---------|--------|-------|
| Panel switching | ✅ | Via toolbar buttons |
| Inspector toggle | ✅ | Animated, persistent |
| File browsing | ✅ | Tree view with actions |
| Node placement | ✅ | Interactive with confirm |
| Canvas navigation | ✅ | Pan, zoom, grid |
| Gesture handling | ✅ | Platform-aware |
| Animations | ✅ | Consistent springs |

## Missing Features

| Feature | Priority | Blocked By |
|---------|----------|------------|
| Document opening | High | WriterDocument missing |
| Node connections | High | 30% remaining work |
| Data persistence | High | Architecture decision |
| Undo/redo | Medium | Command pattern needed |
| Keyboard shortcuts | Medium | Not started |
| Search | Low | UI not designed |
| Settings | Low | No preferences system |

## Navigation Patterns

### Current Implementation
- **Router Pattern**: ContentView as central router
- **Behavior-Contract**: All navigation via configs
- **State Isolation**: Panels independent
- **Platform Adaptive**: Conditional compilation

### Future Considerations
- Coordinator pattern for complex flows
- Deep linking support
- State restoration
- Navigation history

## Known Issues

1. **WriterDocument Missing**: Referenced but doesn't exist
2. **Duplicate State**: ProjectManager created twice
3. **File Opening**: Browser can't open files in editor
4. **Panel Coordination**: No data sharing between panels
5. **Navigation History**: No back/forward support

## Best Practices

### DO
- Use behavior-contract for navigation
- Maintain panel isolation
- Animate state changes
- Platform-specific adaptations
- Consistent spring animations

### DON'T
- Add navigation state to toolbars
- Create circular dependencies
- Skip animations
- Ignore platform differences
- Mix navigation patterns