# StoryWriter Context Engineering Manifest

**System Version**: 1.0.0  
**Last Updated**: 2025-01-08  
**Project State**: Rapid Prototype (4-6 hours)
**Creator**: Tyler Allen
**Created**: 8/26/25

## Module Status

| Module | Version | Status | Last Modified | Health |
|--------|---------|--------|---------------|--------|
| ARCHITECTURE | 1.0.0 | ✅ Active | 2025-01-08 | Good |
| IMPLEMENTATION | 1.0.0 | ✅ Active | 2025-01-08 | Good |
| VISUAL | 1.0.0 | ✅ Active | 2025-01-08 | Good |
| NAVIGATION | 1.0.0 | ✅ Active | 2025-01-08 | Good |
| INTEGRATION | 1.0.0 | ✅ Active | 2025-01-08 | Good |
| EVOLUTION | 1.0.0 | ✅ Active | 2025-01-08 | Good |

## Module Dependencies

```yaml
ARCHITECTURE:
  depends_on: []
  informs: [IMPLEMENTATION, VISUAL, NAVIGATION]
  
IMPLEMENTATION:
  depends_on: [ARCHITECTURE]
  tracks: [project_files, completion_status]
  
VISUAL:
  depends_on: [ARCHITECTURE]
  defines: [ui_components, design_system]
  
NAVIGATION:
  depends_on: [VISUAL, ARCHITECTURE]
  maps: [user_flows, screen_states]
  
INTEGRATION:
  depends_on: [IMPLEMENTATION]
  manages: [external_apis, dependencies]
  
EVOLUTION:
  depends_on: [all_modules]
  records: [decisions, changes]
```

## Quick Load Commands

```bash
# For markdown editor work
Load: IMPLEMENTATION + VISUAL + MarkdownUI sections

# For mapper/canvas work  
Load: IMPLEMENTATION + VISUAL + Mapper sections

# For project management
Load: IMPLEMENTATION + NAVIGATION + ProjectManager

# For architecture decisions
Load: ARCHITECTURE + EVOLUTION
```

## Project Overview

**StoryWriter** is a comprehensive markdown-based writing application for macOS/iOS that combines:
- Advanced markdown editing with custom MarkdownUI implementation
- Visual story mapping with node-based canvas
- Character and story element management
- Project organization with file browser
- Index card system for story planning

## Active Development Areas

1. **Mapper System** - Node-based story visualization
2. **MarkdownUI** - Custom markdown rendering engine
3. **Project Management** - File system integration
4. **Inspector Panels** - Outliner and property inspection

## Module Loading Priority

1. Always load MANIFEST first (this file)
2. Load ARCHITECTURE for understanding system design
3. Load IMPLEMENTATION for current code state
4. Load specific feature modules as needed

## Health Indicators

- ✅ **Good**: Module is complete and current
- ⚠️ **Needs Update**: Module is functional but outdated
- 🔄 **In Progress**: Active development in this module
- ❌ **Critical**: Module needs immediate attention

## Session Management

Current session state tracked in: `.context/dynamic/session.json`
Decision history maintained in: `.context/dynamic/EVOLUTION.md`

## Next Steps

1. Complete initial population of all modules
2. Establish baseline metrics
3. Set up automated documentation updates
4. Create module validation tests