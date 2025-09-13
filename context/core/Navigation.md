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
        ├── CharacterSheet
        ├── IndexWindow
        └── Timeline (Future)
```

## Navigation Control

### Primary Navigation - ProjectToolbar

Using behavior-contract pattern:

```swift
ProjectToolbarConfig:
  selectedSection: () -> ProjectState
  selectSection: (ProjectState) -> Void
```

**Sections (3 panels):**
```swift
selectedCenterButton:
  0 → WriterWindow (pen icon)
  1 → CharacterSheet (person icon)
  2 → IndexWindow (grid icon)
```

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
User clicks pen icon
    → ContentView.selectedCenterButton = 0
    → WriterWindow displays
        → WriterToolbar (functional)
            - Font size controls (11-24pt)
            - Increment/decrement buttons
            - Line numbers toggle
            - Word count display
            - Line count display
            - Dirty state indicator
        → MarkdownWriter (TextEditor now)
        → WriterStatusbar (metrics)

Capabilities:
✅ Adjust font size (11-24pt range)
✅ Toggle line numbers
✅ See word/line count
✅ Track dirty state
✅ Auto-save timer (2 seconds)
⏳ Markdown rendering (MarkdownUI ready)
🔄 Document persistence (WriterDocument exists)
```

### 2. Character Management Flow

```
User clicks person icon
    → ContentView.selectedCenterButton = 1
    → CharacterSheet displays
        → Form sections:
            - Basic Info
            - Physical Description
            - Development
            - Notes
        → Responsive layout (>700pt side-by-side)

Capabilities:
✅ Complete UI form
✅ Responsive design
🎨 Visual design complete
❌ Data persistence
❌ Project integration
```

### 3. Index Card Flow

```
User clicks grid icon
    → ContentView.selectedCenterButton = 2
    → IndexWindow displays
        → Responsive grid (220pt min width)
        → Static example cards

Capabilities:
✅ Responsive layout
🎨 Visual design complete
❌ Card CRUD operations
❌ Data binding
❌ Drag and drop
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

## State Management

### Global State (ContentView)

```swift
@StateObject projectManager = ProjectManager()
@State showInspector = true
@State selectedCenterButton = 0
```

### Panel-Specific State

**WriterWindow:**
```swift
@StateObject state = WriterState()           // View state
@StateObject document = WriterDocument()     // Document model
```

**CharacterSheet:**
```swift
@State character model (not implemented)
// UI state only currently
```

**IndexWindow:**
```swift
// Static cards only
// No state management yet
```

## Animation System

### Consistent Spring Animations

```swift
// Panel transitions
.spring(response: 0.35, dampingFraction: 0.85)

// Card interactions
.spring(response: 0.3, dampingFraction: 0.8)

// Quick feedback
.spring(response: 0.2, dampingFraction: 0.9)

// Divider dragging
.interactiveSpring(response: 0.15, dampingFraction: 1)
```

## Platform Adaptations

### macOS
- Hover states (file items, cards)
- Cursor changes (text, resize)
- Native menu styling
- 320pt inspector width
- Keyboard shortcuts

### iOS/iPadOS
- Touch-optimized targets
- Glass button style for menus
- 280pt inspector width
- Sheet presentations
- Gesture navigation

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
- Project creation dialog
- Save/Open dialogs
- Export options
- Settings/Preferences

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
      case .characters: selectedCenterButton = 1
      case .index: selectedCenterButton = 2
    }
  }
```

### Panel Isolation

Each panel maintains independent state:
- WriterState/WriterDocument for writing
- Character data models (future)
- Index card storage (future)

## Accessibility

### Implemented
- `.help()` tooltips on buttons
- Semantic colors
- Standard SwiftUI accessibility
- Focus indicators (partial)
- Adjustable font sizes (11-24pt)

### Missing
- Complete VoiceOver labels
- Full keyboard navigation
- Tab order definition
- Skip links
- Role definitions

## Performance

### Current Metrics
- Panel switch: < 50ms
- Inspector toggle: Animated ~350ms
- Text editing: Native performance
- File browser: Instant for < 100 items

### Bottlenecks
- File browser reloads entire tree
- No lazy loading
- Synchronous file operations
- No virtualization for large documents

## Working Features

| Feature | Status | Notes |
|---------|--------|-------|
| Panel switching | ✅ | Via toolbar buttons |
| Inspector toggle | ✅ | Animated, persistent |
| File browsing | ✅ | Tree view with actions |
| Text editing | ✅ | With font controls |
| Auto-save timer | ✅ | 2-second delay |
| Dirty state tracking | ✅ | In WriterDocument |
| Animations | ✅ | Consistent springs |

## Missing Features

| Feature | Priority | Blocked By |
|---------|----------|------------|
| File opening | High | Browser-editor connection |
| Save UI | High | No trigger in toolbar |
| Character persistence | High | Data model needed |
| Index card CRUD | High | Data model needed |
| Undo/redo | Medium | Command pattern needed |
| Export | Medium | Not started |
| Search | Low | UI not designed |
| Settings | Low | No preferences system |

## Navigation Patterns

### Current Implementation
- **Router Pattern**: ContentView as central router
- **Behavior-Contract**: All navigation via configs
- **State Isolation**: Panels independent
- **Platform Adaptive**: Conditional compilation

### Future Considerations
- Deep linking support
- State restoration
- Navigation history
- Keyboard shortcuts

## Known Issues

1. **File Opening**: Browser can't open files in editor
2. **Save UI Missing**: No button to trigger saves
3. **Panel Coordination**: Limited data sharing
4. **File Browser Redundancy**: 3 implementations exist
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