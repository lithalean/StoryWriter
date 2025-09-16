# Writer Module Documentation

**Module Version**: 3.0.0  
**Status**: Core Functional  
**Completeness**: 75%  
**Last Updated**: September 2025

## Overview

The Writer module is the heart of StoryWriter, providing the primary text editing experience with a distinctive paper document visual design. Following the January 2025 refactor and September 2025 improvements, the Writer module now features complete save functionality, proper document management, and a clean architectural design.

## Architecture

### Component Structure

```
Writer/
├── WriterWindow.swift      - Main view container with paper document design
├── WriterDocument.swift    - Document model with save/load functionality
└── WriterState.swift       - View state management and editor settings

Core/
├── MarkdownWriter.swift    - Text editing component with line numbers
└── AppBackground.swift     - Canvas background for visual hierarchy
```

### Design Pattern

The Writer module follows a clean separation of concerns:
- **WriterWindow**: Pure view layer with paper document visuals
- **WriterDocument**: Document model handling content, saving, and file operations
- **WriterState**: View state and editor preferences
- **MarkdownWriter**: Reusable text editing component

## Visual Design

### Three-Layer Visual Hierarchy

1. **Background Layer**: Canvas texture (AppBackground)
2. **Document Layer**: Paper document with shadow and rounded corners
3. **Overlay Layer**: Toolbar and statusbar (managed by ContentView)

### Paper Document Appearance

```swift
// Visual specifications
- Corner radius: 16pt (continuous curve)
- Shadow: 20pt blur, 10pt Y offset, 20% black opacity
- Paper color: White (0.98) with light color scheme forced
- Texture: Subtle gradient overlay for paper feel
- Padding: 20pt horizontal, 20pt top, 30pt bottom
```

## Core Components

### WriterWindow

The main container providing the paper document visual metaphor:

**Responsibilities:**
- Renders canvas background
- Creates paper document container
- Manages keyboard shortcuts (Cmd+S on macOS)
- Connects WriterState to WriterDocument

**Key Features:**
- Paper texture with forced light appearance
- Floating document shadow effect
- Responsive to color scheme while maintaining paper look
- Auto-save integration through document binding

### WriterDocument

Complete document management with full save/load functionality:

**Features:**
- ✅ Auto-save with 2-second delay
- ✅ Manual save via toolbar or Cmd+S
- ✅ Save As with native dialogs
- ✅ Dirty state tracking
- ✅ Word and character count
- ✅ Last saved timestamp
- ✅ File path management

**Platform Support:**
- macOS: Native NSSavePanel for save dialogs
- iOS: Document directory saving (picker planned)

**Status Bar Integration:**
```swift
@Published var wordCount: Int
@Published var charCount: Int  
@Published var currentSectionTitle: String?
@Published var lastSaved: Date?
@Published var isSaving: Bool
@Published var editingMode: String // "Draft" or "Revision"
```

### WriterState

Editor preferences and view state management:

**Settings:**
- Font size (11-24pt range, default 15pt)
- Line numbers toggle
- Word wrap toggle
- Syntax highlighting toggle (future)
- Preview mode toggles

**State Management:**
- Cursor position tracking
- Selection range
- Scroll position
- Find/replace state
- Statistics display

**Document Connection:**
- Weak reference to WriterDocument
- Computed properties for stats
- No ownership of document data

### MarkdownWriter

The actual text editing component:

**Current Implementation:**
- Native TextEditor (temporary)
- Monospaced font system
- Optional line numbers
- Dark theme optimized
- Basic text selection

**Planned Enhancement:**
- Integration of 97 MarkdownUI files
- Full markdown rendering
- Syntax highlighting
- Live preview

## Save System

### Auto-Save Flow
```
Content Change → isDirty = true → 2-second timer → autoSave()
                                ↓ (reset on each change)
                          Save to existing path
```

### Manual Save Flow
```
Toolbar/Cmd+S → save() → Has path? → Yes: saveToFile()
                              ↓
                              No: saveAs() → Show dialog → saveToFile()
```

### File Operations

**Save Features:**
- Atomic writes with UTF-8 encoding
- Title extraction from filename
- Modified date tracking
- Error handling (needs user alerts)

**Open/New Operations:**
- Auto-saves before switching documents
- Proper state reset on new document
- Content loading with stats update

## Integration Points

### With ProjectToolbar
- Save button triggers `document.save()`
- Shows dirty state indicator
- Enables/disables save based on `isDirty`

### With ProjectStatusbar
- Provides word/character counts
- Shows last saved time
- Indicates saving state
- Displays current section title

### With ContentView
- Manages document lifecycle
- Coordinates panel switching
- Handles keyboard shortcuts

## Current Limitations

### Critical Issues
- **No Undo/Redo**: Major usability limitation
- **No MarkdownUI**: 97 files waiting for integration
- **Basic Editor**: Using TextEditor instead of custom implementation

### Minor Issues
- iOS save dialog not implemented (uses documents directory)
- No user alerts for save errors
- Section title hardcoded as "Untitled Section"
- No multiple document support

## Keyboard Shortcuts

### Implemented
- **Cmd+S**: Save document (macOS only)

### Planned
- **Cmd+Z/Shift+Cmd+Z**: Undo/Redo
- **Cmd+F**: Find/Replace
- **Cmd+B/I/U**: Bold/Italic/Underline
- **Cmd+]/[**: Increase/Decrease font size

## Performance Characteristics

- **Auto-save delay**: 2 seconds after last edit
- **Content updates**: Immediate with reactive binding
- **Stats calculation**: Real-time on content change
- **Memory usage**: Minimal, single document in memory

## Future Enhancements

### Phase 1: MarkdownUI Integration
- Replace TextEditor with MarkdownUI renderer
- Enable syntax highlighting
- Add live preview mode
- Implement split view

### Phase 2: Advanced Editing
- Undo/redo system
- Find and replace
- Multiple cursors
- Code completion for markdown

### Phase 3: Document Features
- Multiple document tabs
- Document templates
- Version history
- Collaborative editing prep

## Migration Notes

### From Previous Version
- **Removed**: WriterToolbar (consolidated into ProjectToolbar)
- **Added**: WriterDocument with complete save system
- **Added**: Paper document visual design
- **Changed**: State management now separate from document model

### Breaking Changes
- WriterToolbar no longer exists
- Save functionality moved to ProjectToolbar
- Document model required for all operations

## Usage Examples

### Creating a Writer Instance
```swift
struct ContentView: View {
    @StateObject private var document = WriterDocument()
    
    var body: some View {
        WriterWindow()
            .environmentObject(document)
    }
}
```

### Programmatic Save
```swift
// Auto-save on timer
document.updateContent("New content")  // Triggers auto-save

// Manual save
document.save()  // Uses existing path or prompts

// Save with new name
document.saveAs()  // Always shows dialog
```

## Testing Considerations

- Document save/load cycles
- Auto-save timer behavior
- Dirty state tracking
- Platform-specific save dialogs
- Memory management with large documents

## Success Metrics

### Current Achievement (75%)
- ✅ Complete save system
- ✅ Document model
- ✅ Auto-save functionality
- ✅ Visual design complete
- ✅ Keyboard shortcuts (basic)
- ❌ MarkdownUI integration
- ❌ Undo/redo system

### Target (100%)
- Full markdown rendering
- Complete undo/redo
- Find and replace
- All keyboard shortcuts
- iOS document picker