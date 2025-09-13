# StoryWriter Evolution & Decision Log

**Project Created**: 8/26/24 by Tyler Allen  
**Development Time**: 4-6 hours (rapid prototype)  
**Major Refactor**: January 2025 (separation into StoryWriter/StoryMapper)
**Documentation Updated**: 2025-01-08

## Project Genesis

### The Sprint
**Date**: 8/26/24  
**Duration**: 4-6 hours continuous coding  
**Context**: Rapid prototype development  
**Result**: 179 files including complete MarkdownUI library (97 files)

## Actual Development Timeline (Reconstructed)

### Hour 0-1: Foundation
- Created SwiftUI app with SwiftData template
- Set up multi-platform targets (macOS, iOS, tvOS)
- Basic ContentView with panel switching
- Added floating panel styling

### Hour 1-2: MarkdownUI Library
- Appears to have imported/created entire MarkdownUI system
- 97 files with parser, renderer, DSL, themes
- Never actually integrated into the app
- Represents significant effort/time

### Hour 2-3: Core Components
- ProjectManager with JSON serialization
- Basic Writer with TextEditor
- Multiple file browser attempts
- Inspector panel layout

### Hour 3-4: Mapper Implementation
- Full node/edge system
- Canvas with pan/zoom
- Node types with colors
- Visual rendering system

### Hour 4-6: UI Polish & Additional Views
- Character sheet UI
- Index card grid
- Toolbars and status bars
- Material design styling

## January 2025 Refactoring

### The Split Decision
**Date**: January 2025  
**Change**: Separated StoryWriter and StoryMapper into independent applications  
**Files Moved**: 16 mapper-related files → StoryMapper  
**Files Added**: WriterDocument.swift, WriterState.swift

### What Changed

#### Removed from StoryWriter
```
Views/Mapper/          (16 files total)
├── MapperWindow.swift
├── MapperToolbar.swift
├── MapperToolbarConfig.swift
├── MapperCanvas.swift
├── NodeRenderer.swift
├── NodeUI.swift
├── NodeTypes.swift
├── NodeState.swift
├── NodeActionOverlay.swift
├── EdgeRenderer.swift
├── CanvasGestureHandler.swift
├── NodePalette.swift
├── NodeStore.swift
├── EdgeStore.swift
├── MapperState.swift
└── GridOverlay.swift
```

#### Added to StoryWriter
```
Views/Writer/
├── WriterDocument.swift    ✅ Document model (finally!)
└── WriterState.swift       ✅ View state management
```

#### Still Present (Oversight)
```
Location/
└── LocationSheet.swift     ❌ Should have been removed
```

### Rationale for Split
- **Focus**: Each app has clear purpose
- **Complexity**: Mapper was dominating codebase
- **Performance**: Smaller, faster apps
- **Maintenance**: Easier to update independently

### Impact Analysis
- **File Count**: 179 → 163 files
- **Core Files**: ~66 (excluding MarkdownUI)
- **Writer Status**: 20% → 40% complete
- **Critical Issue Resolved**: WriterDocument now exists

## Major Design Decisions

### Decision 1: Single Window Architecture
**Choice**: One window with switchable panels
**Rationale**: Faster to implement, iOS compatibility
**Result**: ✅ Works, simplified after split

### Decision 2: JSON Over SwiftData
**Choice**: Use JSON files despite SwiftData setup
**Rationale**: Quick prototype needs, familiar pattern
**Result**: ✅ Simple but not scalable

### Decision 3: Import MarkdownUI Library
**Choice**: Include 97-file markdown system
**Rationale**: Planned for rendering, future-proofing
**Result**: ⏳ Still not integrated, strategic asset

### Decision 4: Focus on Mapper (Original)
**Choice**: Build complete visual mapper system
**Rationale**: Most interesting feature, visual appeal
**Result**: ✅ So successful it became separate app

### Decision 5: Three File Browsers
**Choice**: Built three different implementations
**Rationale**: Iteration on approach, time pressure
**Result**: ❌ Still confusing redundancy

### Decision 6: Split Applications (2025)
**Choice**: Separate StoryWriter and StoryMapper
**Rationale**: Focus, maintainability, performance
**Result**: ✅ Cleaner architecture, clearer purpose

## Technical Pivots

### TextEditor Instead of MarkdownUI
**Started**: MarkdownUI integration  
**Pivoted To**: Simple TextEditor  
**Status 2025**: Still using TextEditor, MarkdownUI ready

### WriterDocument Implementation
**Original**: Referenced but missing  
**2025**: Finally implemented with auto-save

### Panel Count Reduction
**Original**: 4+ panels (writer, mapper, index, character)  
**2025**: 3 panels (writer, character, index)

## Current State Analysis (Post-Split)

### Stability: IMPROVING
- WriterDocument now exists ✅
- WriterState implemented ✅
- Auto-save timer working ✅
- File browser issues remain ❌

### Completeness: 40%
- Writer: 40% complete (up from 20%)
- Index: 10% complete (UI only)
- Character: 15% complete (UI only)
- File Browser: 50% complete

### Usability: BETTER
- Can edit text with controls
- Font size management works
- Word/line counting functional
- Still can't trigger saves

## Recovery Plan (Updated)

### Phase 1: Consolidate (1 day)
1. ~~Create WriterDocument.swift~~ ✅ DONE
2. Remove 2 redundant file browsers
3. Connect file browser to writer
4. Add save UI trigger
5. Remove Location directory

### Phase 2: Integrate (2 days)
1. Integrate MarkdownUI into writer
2. Character data model & persistence
3. Index card CRUD operations
4. Connect all panels to ProjectManager

### Phase 3: Polish (1 day)
1. Error handling
2. Undo/redo system
3. Export functionality
4. Keyboard shortcuts

## Technical Debt Priority (Updated)

### Critical (Blocks usage)
```yaml
HIGH:
  - No save UI trigger
  - File browser can't open files
  - Location directory still present
```

### Major (Limits features)
```yaml
MEDIUM:
  - Three file browser implementations
  - 97 unused MarkdownUI files (strategic)
  - Character/Index no persistence
  - No undo/redo
```

### Minor (Can wait)
```yaml
LOW:
  - Timeline directory empty
  - Outline panel placeholder
  - Platform inconsistencies
```

## Lessons Learned

### From Original Sprint
1. **Focus wins**: Mapper succeeded due to focus
2. **Libraries costly**: MarkdownUI integration harder than expected
3. **Vertical slices**: Complete features better than UI shells
4. **Time unrealistic**: 4-6 hours too short for full app

### From 2025 Split
1. **Separation good**: Focused apps better than monoliths
2. **Document models critical**: WriterDocument essential
3. **Clean as you go**: Should have removed Location/
4. **Strategic assets**: MarkdownUI valuable for future

## Success Metrics

### Current Score: 4/10
- ✅ Works: Text editing, font controls, project structure
- ❌ Missing: Save UI, file opening, markdown rendering

### Target Score: 7/10
- Markdown fully integrated
- All panels persistent
- Single file browser
- Export functionality

### Version 1.0 Definition
- All panels functional with data
- MarkdownUI rendering active
- Document save/load working
- File browser consolidated
- Export to common formats

## Future Considerations

### MarkdownUI Strategy
- Consider XCFramework for reuse
- Share with SyntaxWriter/LocalLLM projects
- ~2MB overhead worth it for quality

### Architecture Evolution
- SwiftData migration path
- Plugin system for extensions
- Cloud sync consideration
- Collaboration features

### StoryMapper Integration
- Potential re-integration as plugin
- Shared project format
- Cross-app communication
- Unified workspace option