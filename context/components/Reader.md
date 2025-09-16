# Reader Module Documentation

**Module Version**: 1.0.0  
**Status**: Foundation Complete  
**Completeness**: 30%  
**Last Updated**: September 2025  
**Added**: January 2025 Refactor

## Overview

The Reader module provides a distraction-free, book-style reading experience for markdown documents. Currently implemented as a foundation with placeholder functionality, it establishes the architectural pattern for future MarkdownUI integration and sets the stage for a rich reading experience with serif fonts, optimal typography, and potential pagination.

## Purpose

The Reader mode serves as StoryWriter's "presentation view" - a clean, focused environment for reviewing written work without editing capabilities. It's designed to help writers see their work as readers will experience it, with typography optimized for reading rather than editing.

## Architecture

### Component Structure

```
Reader/
├── ReaderWindow.swift    - Main view container for reading experience
└── ReaderState.swift     - State management for reader preferences

Core/
├── MarkdownReader.swift  - Placeholder for future markdown rendering
└── CanvasBackground.swift - Shared canvas texture with Writer
```

### Design Pattern

The Reader module mirrors the Writer module's architecture:
- **ReaderWindow**: View layer with book-style presentation
- **ReaderState**: State management for reading preferences
- **MarkdownReader**: Future integration point for MarkdownUI

## Visual Design

### Reading-Optimized Layout

```swift
// Typography specifications
- Default font size: 18pt (vs 15pt in Writer)
- Font range: 12-28pt (wider than Writer's 11-24pt)
- Font design: Serif (vs Monospaced in Writer)
- Vertical padding: 20pt
- Background: Same canvas texture as Writer
```

### Future Visual Enhancements
- Paper texture background for book feel
- Page margins optimized for reading
- Optional two-column layout
- Page flip animations
- Reading progress indicator

## Core Components

### ReaderWindow

The main container providing the reading experience:

**Current Implementation:**
- Canvas background consistency with Writer
- Full-width markdown display area
- Optional word count overlay
- Serif font system for readability

**Placeholder Features:**
- Stats overlay toggle (word count display)
- Foundation for future pagination
- Space for reading controls

### ReaderState

Reading preferences and document state:

**Font Management:**
```swift
fontSize: CGFloat = 18        // Larger default than Writer
minFontSize: CGFloat = 12     // Higher minimum for readability
maxFontSize: CGFloat = 28     // Higher maximum for accessibility
```

**State Properties:**
- `markdown`: Current document content (string)
- `isLoaded`: Document load state
- `showStats`: Statistics overlay toggle
- `showLineNumbers`: Optional line numbers (default off)
- `wordWrap`: Text wrapping control

**Future Pagination:**
```swift
currentPage: Int = 0    // Current page position
totalPages: Int = 1     // Total page count
```

### MarkdownReader (Placeholder)

Currently a placeholder component, this will be the integration point for the 97 MarkdownUI files:

**Planned Features:**
- Full markdown rendering with MarkdownUI
- Syntax highlighting for code blocks
- Table formatting
- Image embedding
- Link handling
- Custom themes

## Current Functionality

### What Works
- ✅ Basic text display
- ✅ Font size adjustment (12-28pt)
- ✅ Word count calculation
- ✅ Canvas background integration
- ✅ Mode switching from toolbar

### What's Placeholder
- 📝 Actual markdown rendering (plain text only)
- 📝 MarkdownReader component (stub)
- 📝 Pagination system
- 📝 Reading statistics
- 📝 Export functionality

## Integration Points

### With ProjectToolbar
- Reader button switches to reading mode
- Maintains visual consistency
- Future: Export and print options

### With ContentView
- Seamless switching between Writer and Reader
- Shared document model (future)
- Consistent panel layout

### Future Document Integration
```swift
// Planned connection to WriterDocument
func loadFromWriter(_ document: WriterDocument) {
    state.loadMarkdown(document.content)
}
```

## MarkdownUI Integration Strategy

### Phase 1: Basic Rendering
1. Connect MarkdownReader to MarkdownUI library
2. Enable basic markdown parsing
3. Apply default theme

### Phase 2: Reading Optimization
1. Custom typography theme
2. Optimal line height and spacing
3. Serif font integration
4. Code block syntax highlighting

### Phase 3: Advanced Features
1. Table of contents generation
2. Pagination with page memory
3. Reading time estimation
4. Export to PDF/ePub

## Typography Considerations

### Reading vs Writing Fonts
```swift
// Writer (editing focus)
.system(size: 15, design: .monospaced)

// Reader (reading comfort)
.system(size: 18, design: .serif)
```

### Accessibility
- Larger font size range (12-28pt)
- High contrast text
- Adjustable line spacing (future)
- Dyslexia-friendly font option (future)

## Performance Characteristics

- **Load time**: Instant (string-based currently)
- **Font scaling**: Real-time adjustment
- **Memory usage**: Minimal (single document)
- **Future concern**: Pagination calculation for large documents

## Keyboard Shortcuts

### Planned
- **Cmd+R**: Toggle Reader mode
- **Cmd+Plus/Minus**: Adjust font size
- **Space**: Page down (when paginated)
- **Shift+Space**: Page up (when paginated)
- **Cmd+P**: Print document

## Future Enhancements

### Near Term (MarkdownUI Integration)
- Full markdown rendering
- Syntax highlighting
- Image support
- Link navigation
- Table formatting

### Medium Term (Reading Features)
- Pagination with smooth scrolling
- Table of contents sidebar
- Reading progress tracking
- Bookmarks and annotations
- Print preview and formatting

### Long Term (Advanced)
- Export to PDF/ePub/DOCX
- Custom CSS themes
- Two-column layout option
- Full-screen presentation mode
- Reading statistics dashboard

## Comparison with Writer Mode

| Feature | Writer | Reader |
|---------|--------|--------|
| Purpose | Editing | Reading |
| Font | Monospaced | Serif |
| Default Size | 15pt | 18pt |
| Size Range | 11-24pt | 12-28pt |
| Line Numbers | Optional | Hidden |
| Editable | Yes | No |
| Focus | Creation | Consumption |

## Usage Examples

### Loading Content into Reader
```swift
// Current (manual)
readerState.loadMarkdown(markdownContent)

// Future (from Writer)
readerState.loadFromDocument(writerDocument)
```

### Adjusting Reading Preferences
```swift
// Font size adjustment
readerState.increaseFontSize()  // +1pt up to 28pt
readerState.decreaseFontSize()  // -1pt down to 12pt

// Toggle statistics
readerState.showStats.toggle()

// Reset to defaults
readerState.resetView()
```

## Testing Considerations

- Font size boundaries (12-28pt)
- Large document handling
- Mode switching performance
- Memory management
- Future: Pagination accuracy

## Success Metrics

### Current Achievement (30%)
- ✅ Basic structure implemented
- ✅ Mode switching works
- ✅ Font controls functional
- ✅ Visual consistency with Writer
- ❌ No markdown rendering
- ❌ No MarkdownUI integration
- ❌ No pagination

### Target (100%)
- Full MarkdownUI rendering
- Smooth pagination
- Export functionality
- Reading statistics
- Accessibility features
- Custom themes

## Migration Notes

### From Writer Mode
- Shared canvas background
- Consistent toolbar integration
- Future: Shared document model
- Different typography focus

### Breaking Changes
None - Reader is a new module as of January 2025

## Known Limitations

1. **No Markdown Rendering**: Currently displays plain text only
2. **No Document Connection**: Not connected to WriterDocument
3. **No Export**: Cannot export or print
4. **No Pagination**: Scrolling only
5. **No Themes**: Fixed appearance

## Development Priority

While Reader mode provides value as a distraction-free reading environment, the priority remains on integrating MarkdownUI which will transform both Writer and Reader modes simultaneously. The Reader module's clean architecture positions it well for this enhancement.