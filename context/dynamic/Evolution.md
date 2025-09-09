# StoryWriter Evolution & Decision Log

**Project Created**: 8/26/25 by Tyler Allen  
**Development Time**: 4-6 hours (rapid prototype)  
**Documentation Created**: 2025-01-08

## Project Genesis

### The Sprint
**Date**: 8/26/25  
**Duration**: 4-6 hours continuous coding  
**Context**: Rapid prototype development  
**Result**: 169 files including complete MarkdownUI library (97 files)

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

## Major Design Decisions (Inferred)

### Decision 1: Single Window Architecture
**Choice**: One window with switchable panels

**Likely Rationale**:
- Faster to implement
- Better iOS compatibility
- Simpler state management
- Time constraint (4-6 hours)

**Result**: ✅ Works but limits functionality

---

### Decision 2: JSON Over SwiftData
**Choice**: Use JSON files despite SwiftData setup

**Likely Rationale**:
- SwiftData complexity
- Quick prototype needs
- Familiar pattern
- Direct file control

**Result**: ✅ Simple but not scalable

---

### Decision 3: Import MarkdownUI Library
**Choice**: Include 97-file markdown system

**Likely Rationale**:
- Planned to use for rendering
- Found existing library
- Future-proofing

**Result**: ❌ Never integrated, wasted effort

---

### Decision 4: Focus on Mapper
**Choice**: Build complete visual mapper system

**Likely Rationale**:
- Most interesting feature
- Visual appeal
- Differentiator
- Fun to build

**Result**: ✅ Most complete feature

---

### Decision 5: Three File Browsers
**Choice**: Built three different file browser implementations

**Likely Rationale**:
- Iteration on approach
- Platform differences
- Unclear requirements
- No time to refactor

**Result**: ❌ Confusing redundancy

## Technical Pivots During Development

### TextEditor Instead of MarkdownUI
**Started**: MarkdownUI integration  
**Pivoted To**: Simple TextEditor  
**Why**: Time pressure, complexity  
**Impact**: 97 unused files

### Multiple File Browser Attempts
**Attempt 1**: FileSystem.swift (complex, platform-aware)  
**Attempt 2**: FileBrowser.swift (simpler)  
**Attempt 3**: FileSystemModel.swift (view model)  
**Why**: Each attempt tried different approach  
**Impact**: Unclear which is active

## What Went Right

### Successful Implementations
1. **Mapper System** - Fully functional, visually appealing
2. **Project Structure** - Clean JSON model
3. **Panel UI** - Smooth switching, good looking
4. **Platform Setup** - Multi-platform from start
5. **Visual Design** - Consistent material style

### Good Architectural Choices
- SwiftUI throughout
- ObservableObject pattern
- Codable models
- Platform conditionals
- Component separation

## What Went Wrong

### Failed Integrations
1. **MarkdownUI** - 97 files never used
2. **SwiftData** - Set up but abandoned
3. **WriterDocument** - Referenced but never created

### Incomplete Features
1. **Writer** - No actual markdown functionality
2. **Index Cards** - Static UI only
3. **Character Sheet** - No persistence
4. **File Browser** - Doesn't open files

### Architectural Issues
1. **Three file browsers** - Redundant code
2. **No data flow** - Panels disconnected
3. **Missing core** - No document model
4. **No persistence** - Most data ephemeral

## Time Analysis

### Time Well Spent (~3 hours)
- Mapper implementation
- Basic app structure
- UI styling
- Project model

### Time Wasted (~2 hours)
- MarkdownUI library (never used)
- Multiple file browsers
- SwiftData setup (abandoned)
- Non-functional UI

### Missing Critical Features (~1 hour needed)
- Save/load UI
- Document model
- Data persistence
- Error handling

## Lessons Learned

### About Rapid Prototyping
1. **Focus on one thing** - Mapper succeeded because focused effort
2. **Don't import large libraries** - MarkdownUI waste
3. **Build vertical slices** - Complete features, not UI shells
4. **Defer complexity** - TextEditor was right choice

### About Architecture
1. **Start simple** - Single window was correct
2. **Don't premix patterns** - SwiftData + JSON confused
3. **One implementation** - Three file browsers bad
4. **Connect early** - Disconnected panels problematic

### About Time Management
1. **4-6 hours too short** - For complete app
2. **Polish vs function** - Chose polish (materials, animations)
3. **Documentation debt** - Accumulated immediately
4. **Testing impossible** - No time for any tests

## Current State Analysis

### Stability: FRAGILE
- Missing core components (WriterDocument)
- No error handling
- No data persistence
- Untested code

### Completeness: 30%
- Mapper: 90% complete
- Writer: 20% complete
- Index: 10% complete
- Character: 15% complete
- File Browser: 50% complete

### Usability: LIMITED
- Can view UI
- Can play with mapper
- Can type text
- Cannot save work

## Recovery Plan

### Phase 1: Stabilize (2 hours)
1. Create WriterDocument.swift
2. Remove 2 file browsers
3. Connect one file browser
4. Add basic save/load

### Phase 2: Complete Core (4 hours)
1. Integrate OR remove MarkdownUI
2. Connect panels to ProjectManager
3. Add persistence to all components
4. Implement auto-save

### Phase 3: Polish (2 hours)
1. Error handling
2. Empty states
3. Loading states
4. Keyboard shortcuts

## Technical Debt Priority

### Critical (Breaks app)
```yaml
HIGH:
  - WriterDocument.swift missing
  - No save functionality
  - No undo/redo
```

### Major (Limits use)
```yaml
MEDIUM:
  - Three file browsers
  - 97 unused MarkdownUI files
  - Disconnected panels
  - No persistence
```

### Minor (Cosmetic)
```yaml
LOW:
  - Empty toolbars
  - Static index cards
  - Unused SwiftData
  - Platform inconsistencies
```

## Retrospective Questions

### Why MarkdownUI but TextEditor?
Likely imported library early, realized integration complexity, pivoted to TextEditor to make progress, never cleaned up.

### Why three file browsers?
Probably tried different approaches for different needs, ran out of time to consolidate.

### Why focus on Mapper?
Most interesting technically, most visual impact, most fun to build.

### Why missing saves?
Ran out of time after UI work, save/load less interesting than features.

## Future Architecture Recommendations

### If Starting Over
1. **Start with document model** - WriterDocument first
2. **One file browser** - Design once, use everywhere
3. **Skip MarkdownUI** - Until core works
4. **Vertical slices** - One complete feature at a time
5. **Save early** - Data persistence from start

### For Current Code
1. **Delete unused code** - MarkdownUI, extra browsers
2. **Create missing pieces** - WriterDocument
3. **Connect components** - Through ProjectManager
4. **Add saves** - Before any new features
5. **Then enhance** - Markdown, etc.

## Success Metrics

### What Success Looks Like
- Can create, edit, save documents
- Mapper saves with project
- All panels connected to data
- No duplicate implementations
- No unused code

### Current Score: 3/10
- Works: Basic UI, mapper viz, typing
- Doesn't: Save, load, persist, integrate

### Target Score: 7/10
- Stable core
- Feature complete
- Data persistent
- Code clean
- Room to grow