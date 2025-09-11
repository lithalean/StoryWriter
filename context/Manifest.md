# StoryWriter Context Engineering Manifest

**System Version**: 2.0.0  
**Last Updated**: 2025-01-08  
**Project State**: Active Development (Post-Prototype)
**Creator**: Tyler Allen
**Initial Sprint**: 8/26/25 (4-6 hours)
**Current Status**: ~30% Complete, Architecture Stable

## Module Status

| Module | Version | Status | Completeness | Health |
|--------|---------|--------|--------------|--------|
| ARCHITECTURE | 2.0.0 | ✅ Active | 95% | Good - Behavior-contract pattern complete |
| IMPLEMENTATION | 2.0.0 | 🔄 Active | 30% | Major - Critical components missing |
| VISUAL | 2.0.0 | ✅ Active | 90% | Good - Glass-morphism system complete |
| NAVIGATION | 2.0.0 | ✅ Active | 75% | Good - Single-window architecture working |
| INTEGRATION | 1.0.0 | 📋 Planned | 5% | Future - Mostly specifications |
| EVOLUTION | 2.0.0 | ✅ Active | N/A | Good - Comprehensive history |
| NODE | 1.0.0 | ✅ Active | 80% | Good - 19 types, connections 70% |
| TOOLBAR | 1.0.0 | ✅ Active | 100% | Excellent - Pattern complete |
| MARKDOWNUI | 1.0.0 | ⏳ Ready | 100% | Strategic - Future use in 3 projects |

## Module Dependencies

```yaml
ARCHITECTURE:
  depends_on: []
  informs: [IMPLEMENTATION, VISUAL, NAVIGATION, NODE, TOOLBAR]
  pattern: behavior-contract
  
IMPLEMENTATION:
  depends_on: [ARCHITECTURE]
  tracks: [project_files, completion_status]
  critical_missing: [WriterDocument]
  
VISUAL:
  depends_on: [ARCHITECTURE]
  defines: [ui_components, design_system]
  status: glass-morphism complete
  
NAVIGATION:
  depends_on: [VISUAL, ARCHITECTURE]
  maps: [user_flows, screen_states]
  pattern: single-window-panel-switching
  
NODE:
  depends_on: [VISUAL, ARCHITECTURE]
  features: [19_types, placement, dragging]
  incomplete: [connections_30%, persistence]
  
TOOLBAR:
  depends_on: [ARCHITECTURE]
  exemplifies: behavior-contract
  status: production-ready
  
MARKDOWNUI:
  depends_on: []
  files: 97
  status: complete_but_unintegrated
  future_projects: [StoryWriter, SyntaxWriter, LocalLLM]
  strategy: potential_XCFramework
  
INTEGRATION:
  depends_on: [IMPLEMENTATION]
  manages: [external_apis, dependencies]
  status: mostly_planned
  
EVOLUTION:
  depends_on: [all_modules]
  records: [decisions, changes, technical_debt]
```

## Quick Load Commands

```bash
# For current mapper work (YOUR FOCUS)
Load: IMPLEMENTATION + NODE + MapperWindow sections

# For upcoming writer integration
Load: IMPLEMENTATION + MARKDOWNUI + WriterWindow

# For architecture review
Load: ARCHITECTURE + TOOLBAR + EVOLUTION

# For visual polish
Load: VISUAL + NODE styling sections

# For understanding issues
Load: EVOLUTION + IMPLEMENTATION status
```

## Project Overview

**StoryWriter** is a comprehensive markdown-based writing application for macOS/iOS that combines:
- Advanced markdown editing with custom MarkdownUI implementation (ready to integrate)
- Visual story mapping with node-based canvas (90% complete)
- Character and story element management (UI only)
- Project organization with file browser (50% functional)
- Index card system for story planning (UI only)

## Actual Component Status

### ✅ Working Components
- **Mapper System** (90%): Nodes work, connections 70%, no persistence
- **Toolbar System** (100%): Perfect behavior-contract implementation
- **Project Manager** (100%): JSON save/load functional
- **File Browser** (50%): Browse works, can't open files
- **Visual Design** (90%): Complete glass-morphism system

### 🔄 Partially Working
- **Writer** (20%): TextEditor only, MarkdownUI ready but not integrated
- **Node Connections** (70%): In progress
- **Panel Coordination** (40%): Panels work but disconnected

### 🎨 UI Complete (No Backend)
- **Character Sheet** (15%): Full UI, no data persistence
- **Index Cards** (10%): Static grid only
- **Timeline** (0%): Directory exists, nothing implemented
- **Outline Panel** (0%): Placeholder in ContentView

### ⚠️ Critical Issues
- **WriterDocument.swift**: Referenced everywhere but DOESN'T EXIST
- **Three File Browsers**: Confusing redundancy (FileSystem, FileBrowser, FileSystemModel)
- **No Save UI**: Projects can save but no UI to trigger it
- **No Undo/Redo**: Critical for any editor

## Active Development Areas

### Current Sprint Focus
1. **Complete Mapper Connections** (30% remaining)
2. **Add Node Persistence** to project files
3. **Create WriterDocument** model

### Next Phase (Post-Mapper)
1. **Integrate MarkdownUI** into Writer
2. **Consolidate File Browsers** to single implementation
3. **Connect Panels** through ProjectManager
4. **Implement Auto-save**

## Technical Debt Priority

### 🔴 Critical (Blocks Usage)
- WriterDocument.swift missing
- No save functionality in UI
- Node data not persisted

### 🟡 Major (Limits Features)
- Three file browser implementations
- Disconnected panels
- No document model
- Missing data flow

### 🟢 Minor (Can Wait)
- 97 MarkdownUI files (strategic investment, not debt)
- Unused SwiftData scaffolding
- Empty Timeline/Outline views

## Module Loading Priority

1. **Always load MANIFEST first** (this file)
2. **For current work**: Load NODE + relevant MapperWindow sections
3. **For architecture**: Load ARCHITECTURE + TOOLBAR (best examples)
4. **For issues**: Load EVOLUTION (comprehensive history)
5. **For future**: Load MARKDOWNUI specs when ready to integrate

## Health Indicators

- ✅ **Good**: Module is complete and current
- 🔄 **In Progress**: Active development in this module
- 🎨 **UI Only**: Visual complete, needs backend
- ⏳ **Ready**: Complete but waiting for integration
- ⚠️ **Critical**: Module needs immediate attention
- 📋 **Planned**: Specification only

## Performance Metrics

- **Node Rendering**: 60fps up to ~100 nodes (500 target)
- **Panel Switching**: < 50ms
- **File Operations**: Synchronous (needs async)
- **Memory**: ~45MB baseline, ~78MB with 500 nodes

## Time Investment

### Original Sprint (4-6 hours)
- Foundation + MarkdownUI import: 2 hours
- Mapper implementation: 2 hours
- UI polish: 1-2 hours

### Estimated to Complete
- Core stabilization: 2-4 hours
- Writer integration: 4-6 hours
- Full feature complete: 20-30 hours

## Next Steps

1. **Immediate** (You are here):
   - Complete mapper connections
   - Add node persistence
   - Create WriterDocument

2. **Short Term**:
   - Integrate MarkdownUI
   - Consolidate file browsers
   - Connect panels to data

3. **Strategic**:
   - Consider XCFramework for MarkdownUI
   - Plan SyntaxWriter/LocalLLM shared components
   - Design plugin architecture

## Success Metrics

### Current Score: 3/10
- ✅ Works: UI navigation, mapper visualization, basic typing
- ❌ Doesn't: Save UI, load files, persist nodes, render markdown

### Target Score: 7/10
- Stable core with saves
- MarkdownUI integrated
- All panels connected
- Data persistent
- Clean architecture

### Version 1.0 Definition
- All panels functional with data
- Document save/load working
- Markdown rendering active
- Node persistence complete
- Single file browser implementation