# StoryWriter Navigation & User Flows

**Version**: 3.0.0  
**Architecture**: Single-window with mode switching  
**Pattern**: Behavior-contract navigation  
**Platform Support**: macOS, iOS, tvOS  
**Last Updated**: September 2025

## Navigation Architecture

### Simplified Two-Mode Design

StoryWriter uses a **single-window architecture** with two primary modes and toggleable panels:

```
StoryWriterApp
    ↓
ContentView (Router)
    ├── ProjectToolbar (Unified controls)
    ├── Inspector Panel (Left sidebar - toggleable)
    ├── Main Content (Mode-based)
    │   ├── WriterWindow
    │   └── ReaderWindow
    ├── Formatter Panel (Right sidebar - toggleable)
    └── ProjectStatusbar (Bottom overlay)
```

## Navigation Control

### Primary Navigation - ProjectToolbar

Unified toolbar with all primary controls:

```swift
ProjectToolbarConfig:
  // Mode switching (center)
  selectedSection: () -> ProjectState
  selectSection: (ProjectState) -> Void
  
  // Panel toggles (left/right)
  isInspectorVisible: () -> Bool
  toggleSidebar: () -> Void
  isRightPanelVisible: () -> Bool
  toggleRightPanel: () -> Void
  
  // Save functionality (right)
  isDirty: () -> Bool
  save: () -> Void
  canSave: () -> Bool
```

**Modes (2 states):**
```swift
enum ProjectState {
  case writer  // Document editing (doc icon)
  case reader  // Reading view (book icon)
}
```

## User Flows

### 1. Writing Flow (Primary)

```
User clicks doc icon or starts in Writer mode
    → ContentView.currentSection = .writer
    → WriterWindow displays with paper document
        → TextEditor (temporary, MarkdownUI ready)
        → Auto-save timer (2 seconds)
        → Save via toolbar button or Cmd+S
    → FormatterPanel available (right toggle)
        → Font size controls
        → Bold/Italic/Underline
        → Text alignment
        → Line numbers toggle

Capabilities:
✅ Full text editing
✅ Complete save system (auto + manual)
✅ Font formatting controls
✅ Line numbers display
✅ Word/character counting
✅ Paper document design
⏳ Markdown rendering (97 files ready)
❌ Undo/Redo system
```

### 2. Reading Flow

```
User clicks book icon
    → ContentView.currentSection = .reader
    → ReaderWindow displays
        → Serif typography (18pt default)
        → Larger font range (12-28pt)
        → Read-only view
        → Canvas background

Capabilities:
✅ Mode switching works
✅ Font size adjustment
✅ Optimized typography
⏳ Markdown rendering (ready to integrate)
❌ Pagination
❌ Export options
```

### 3. Save Flow (Complete)

```
Document edited
    → isDirty = true
    → Save button shows filled icon
    → User clicks save or presses Cmd+S
        → Native save dialog (first time)
        → Direct save (existing file)
    → Auto-save after 2 seconds of inactivity
    → Statusbar shows "Saved just now"

Save States:
✅ Manual save via button
✅ Keyboard shortcut (Cmd+S)
✅ Auto-save timer
✅ Save As functionality
✅ Dirty state tracking
```

### 4. Inspector/File Browser Flow

```
User toggles left sidebar
    → showInspector = true/false
    → InspectorPanel displays
        → FileBrowser (single implementation)
        → Three folders:
            - Manuscript
            - Characters  
            - Locations
        → "Open in Finder" on macOS

Capabilities:
✅ Browse file structure
✅ Visual hierarchy
✅ Open in Finder
❌ Open files in editor
❌ Create/Delete (removed for simplicity)
```

### 5. Formatter Panel Flow

```
User toggles right sidebar
    → showFormatter = true/false
    → FormatterPanel displays
        → Font size slider
        → Text formatting buttons
        → Alignment controls
        → Line numbers toggle

Capabilities:
✅ All controls functional
✅ Real-time updates
✅ Settings persistence
```

## State Management

### Global State (ContentView)

```swift
// Navigation
@State currentSection: ProjectState = .writer
@State showInspector = true
@State showFormatter = false

// Document
@StateObject document = WriterDocument()
@StateObject projectManager = ProjectManager()

// Statusbar preferences (persisted)
@AppStorage("statusBar.showCharCount") showCharCount = false
@AppStorage("statusBar.showEditingMode") showEditingMode = true
@AppStorage("statusBar.showSection") showSection = true
```

### Mode-Specific State

**WriterWindow:**
```swift
@StateObject state = WriterState()      // View preferences
@StateObject document = WriterDocument() // Document with save
```

**ReaderWindow:**
```swift
@StateObject state = ReaderState()      // Reading preferences
// Future: shares document with Writer
```

## Panel Layout System

### Three-Panel Architecture

```
┌─────────┬──────────────────┬──────────┐
│Inspector│   Main Content   │Formatter │
│ (Left)  │  (Writer/Reader) │ (Right)  │
├─────────┴──────────────────┴──────────┤
│           Status Bar                   │
└────────────────────────────────────────┘
```

### Panel States
- **Both panels**: Inspector + Content + Formatter
- **Left only**: Inspector + Content
- **Right only**: Content + Formatter  
- **Neither**: Content only (maximized)

## Animation System

### Consistent Spring Animations

```swift
// Panel toggles
.spring(response: 0.35, dampingFraction: 0.85)

// Mode transitions
.spring(response: 0.3, dampingFraction: 0.85)

// Save button feedback
.scaleEffect(pressed ? 0.97 : 1.0)
```

## Platform Adaptations

### macOS
- Native save panels (NSSavePanel)
- Keyboard shortcuts (Cmd+S)
- "Open in Finder" support
- Hover states
- 52pt toolbar height

### iOS/iPadOS
- Document directory saving
- Touch-optimized controls
- Glass button styling
- Sheet presentations
- Adaptive layouts

### tvOS
- Simplified interactions
- Focus engine support
- Fixed 1920x1080 layout
- Remote control optimized

## Data Flow

### Behavior-Contract Navigation

All navigation through closures:

```swift
// Toolbar receives behaviors only
ProjectToolbar(config: ProjectToolbarConfig(
    selectedSection: { currentSection },
    selectSection: { section in
        currentSection = section
    },
    save: { document.save() },
    isDirty: { document.isDirty }
))

// Statusbar receives data through closures
ProjectStatusbar(config: ProjectStatusbarConfig(
    wordCount: { document.wordCount },
    lastSaved: { document.lastSaved }
))
```

### Document Flow

```
WriterDocument (source)
    ↓ @Published
ContentView (coordinator)
    ↓ Closures
Toolbar/Statusbar (display)
```

## Accessibility

### Implemented
- `.help()` tooltips on all buttons
- Semantic button labels
- Adjustable font sizes (11-28pt range)
- High contrast borders
- Standard SwiftUI accessibility

### Missing
- Complete VoiceOver optimization
- Full keyboard navigation
- Custom accessibility actions
- Reading progress announcements

## Performance

### Current Metrics
- Mode switch: < 50ms
- Panel toggle: ~350ms animated
- Save operation: < 100ms typical
- Auto-save delay: 2000ms
- Text editing: Native performance

### Optimizations
- Spring animations GPU-accelerated
- Lazy panel loading
- Efficient state updates
- Minimal redraws

## Working Features

| Feature | Status | Notes |
|---------|--------|-------|
| Mode switching | ✅ | Writer/Reader toggle |
| Save system | ✅ | Complete with auto-save |
| Panel toggles | ✅ | Inspector/Formatter |
| Statusbar | ✅ | Full info display |
| File browsing | ✅ | Single implementation |
| Text editing | ✅ | With formatting controls |
| Keyboard shortcuts | ✅ | Cmd+S implemented |

## Missing Features

| Feature | Priority | Implementation |
|---------|----------|----------------|
| MarkdownUI | Critical | 97 files ready |
| Undo/Redo | High | Not started |
| File opening | Medium | Browser disconnected |
| Character data | Medium | UI only |
| Export | Medium | Not started |
| Find/Replace | Low | Not designed |
| Templates | Low | Not planned |

## Navigation Patterns

### Current Implementation
- **Two-Mode System**: Writer and Reader
- **Unified Toolbar**: All controls in one place
- **Behavior-Contract**: Navigation via closures
- **Panel Independence**: Each panel self-contained

### Removed Complexity
- ~~WriterToolbar~~: Merged into ProjectToolbar
- ~~4-Panel System~~: Simplified to 2 modes
- ~~Multiple File Browsers~~: Single implementation
- ~~Character/Index Navigation~~: Shelved for now

## Known Issues

1. **File Opening**: Browser can't open files in editor
2. **Section Title**: Hardcoded as "Untitled Section"
3. **No Undo/Redo**: Critical for editing
4. **No Templates**: New documents start blank

## Best Practices

### DO
- Use behavior-contract for all navigation
- Maintain mode isolation
- Apply consistent animations
- Test platform differences
- Keep toolbar unified

### DON'T
- Add state to navigation components
- Create navigation dependencies
- Skip animations
- Mix navigation patterns
- Split toolbar functionality

## Migration from Previous Version

### What Changed
- **Toolbar**: WriterToolbar removed, all in ProjectToolbar
- **Navigation**: 4 panels reduced to 2 modes
- **Save**: Now complete with button and auto-save
- **File Browser**: 3 implementations reduced to 1
- **Statusbar**: Added for information display

### What Stayed
- Behavior-contract pattern
- Single-window architecture
- Panel toggle system
- Spring animations
- Platform adaptations

## Success Metrics

### Navigation System (90% Complete)
- ✅ Mode switching smooth
- ✅ Panel toggles working
- ✅ Save integration complete
- ✅ Statusbar functional
- ✅ Keyboard shortcuts
- ❌ Undo/Redo navigation

The navigation system has been significantly simplified and improved, with a focus on the core writing experience and clean mode separation.