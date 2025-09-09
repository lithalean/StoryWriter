# Node System

## Overview

The node system provides visual story elements on an infinite canvas. With 19 built-in types across 4 categories, nodes represent characters, locations, quests, and system logic in an interconnected story map.

## Architecture

### Core Components

```
NodeTypes (enum)          # Type definitions
    ↓
StoryNode (model)        # Data structure
    ↓
NodeState (enum)         # UI states
    ↓
NodeUI (view)           # Composable renderer
    ↓
NodeActionOverlay       # Context actions
```

### Key Files

```
Views/Mapper/
├── Node.swift              # Legacy component
├── NodeTypes.swift         # Type definitions (19 types)
├── NodeStyle.swift         # Visual styling system
├── NodeState.swift         # State machine
├── NodeUI.swift           # Main node view
├── NodeActionOverlay.swift # Action system
├── NodeRenderer.swift      # Canvas renderer
├── NodePalette.swift      # Type provider
└── StoryFiles.swift       # Data models
```

## Node Types

### Categories

**Story** (Blue/Purple)
- `character` - Characters and NPCs
- `dialogue` - Conversations
- `cutscene` - Cinematics
- `choice` - Branching decisions

**World** (Green)
- `location` - Specific places
- `region` - Areas
- `landmark` - Notable features

**Gameplay** (Orange/Red)
- `quest` - Missions
- `objective` - Goals
- `combat` - Battles
- `puzzle` - Challenges
- `item` - Collectibles
- `weapon` - Equipment
- `keyItem` - Critical items

**System** (Gray)
- `trigger` - Events
- `condition` - Logic
- `variable` - State
- `event` - System events

### Type Properties

Each type provides:
- Display name and SF Symbol icon
- Default color and size
- Category assignment
- Connection rules (multiple allowed, max connections)

## Data Model

### StoryNode

```swift
struct StoryNode {
    let id: UUID
    var title: String
    var type: NodeTypes
    var position: CGPoint
    var content: String
    var tags: [String]
    var isExpanded: Bool
    
    // Type-specific
    var dialogueOptions: [String]
    var questObjectives: [String]
    var characterTraits: [String]
    
    // Metadata
    var createdAt: Date
    var modifiedAt: Date
    var connections: [UUID]
}
```

### StoryEdge

```swift
struct StoryEdge {
    let id: UUID
    let fromID: UUID
    let toID: UUID
    var type: EdgeTypes
    var label: String?
    var isBidirectional: Bool
}
```

## Node States

### State Machine

```swift
enum NodeState {
    case normal
    case placement(onConfirm, onCancel)
    case editing(onSave, onCancel)
    case connecting(target)
    case custom(String)
}
```

### State Transitions

```
normal → placement → normal (confirm/cancel)
normal → editing → normal (save/cancel)
normal → connecting → normal (complete/cancel)
```

## UI Components

### NodeUI

Main composable view with action overlays:

```swift
NodeUI<Actions: View>(
    data: StoryNode,
    nodeState: NodeState,
    isSelected: Bool,
    isHovered: Bool,
    isDragging: Bool,
    onDelete: () -> Void,
    actionsOverlay: Actions?
)
```

### Sub-Components

**NodeHeader**
- Icon badge
- Title text
- Delete button (hover)

**NodeContent**
- Description
- Type-specific content
- Dialogue options, objectives, traits

**NodeTags**
- Horizontal scrolling
- Capsule-styled chips

### NodeActionOverlay

Context-sensitive actions:

**Action Protocol:**
```swift
protocol NodeAction {
    var icon: String { get }
    var color: Color { get }
    var action: () -> Void { get }
}
```

**Standard Actions:**
- `ConfirmAction` (green checkmark)
- `CancelAction` (red X)
- `DeleteAction` (red trash)

**Layouts:**
- Horizontal (default)
- Vertical (space-constrained)
- Corner (minimal)
- Floating (emphasis)

## Styling

### NodeStyle

Configuration based on state:

```swift
NodeStyle(
    type: NodeTypes,
    mode: DisplayMode,  // compact/normal/expanded
    isSelected: Bool,
    isHovered: Bool,
    isDragging: Bool,
    colorScheme: ColorScheme
)
```

### Display Modes

**Compact**
- Minimal height
- Large icon (33pt)
- No content preview

**Normal**
- Standard display
- Shows header/tags
- 14-16pt icons

**Expanded**
- Full content visible
- All metadata shown

### Visual States

| State | Opacity | Border | Shadow | Scale |
|-------|---------|--------|--------|-------|
| Normal | 0.85 | 1.0 | 5 | 1.0 |
| Hovered | 0.9 | 1.5 | 8 | 1.03 |
| Selected | 0.85 | 2.5 | 10 | 1.0 |
| Dragging | 0.95 | 2.5 | 15 | 1.08 |

## Placement System

### Interactive Flow

1. User selects type from toolbar
2. `pendingAdd` state activated
3. Canvas tap creates pending node
4. Node appears with confirm/cancel
5. Drag to position
6. Confirm adds to store

### Implementation

```swift
// MapperWindow
@State private var pendingAdd: NodeTypes?
@State private var pendingNode: StoryNode?

// Create pending
if let type = pendingAdd {
    pendingNode = StoryNode.create(
        title: "New \(type.displayName)",
        type: type,
        at: canvasPosition
    )
}

// Display with actions
NodeUI(
    data: pendingNode,
    nodeState: .placement(
        onConfirm: { nodeStore.addNode(pendingNode) },
        onCancel: { pendingNode = nil }
    )
)
```

## Canvas Integration

### NodeRenderer

Bridge between data model and canvas:
- Handles selection
- Manages dragging
- Snap-to-grid support
- Platform-specific hover (macOS)

### Stores

**NodeStore**
```swift
@Published var nodes: [StoryNode]
@Published var selectedNodeID: UUID?

func addNode(_ node)
func removeNode(id: UUID)
func updateNodePosition(id, position, snapToGrid)
```

**EdgeStore**
```swift
@Published var edges: [StoryEdge]
@Published var selectedEdgeID: UUID?

func addEdge(_ edge)
func removeEdgesConnectedTo(nodeID)
```

## Platform Features

### macOS
- Hover states
- Delete button on hover
- Right-click menus
- Multi-select (Cmd/Shift)

### iOS/iPadOS
- Touch-and-hold menus
- Pinch zoom
- Two-finger pan
- Haptic feedback

### tvOS
- Focus engine
- Simplified interactions
- Remote control

## Performance

### Optimization
- Lazy rendering (visible only)
- View recycling
- Batch updates
- Async content loading
- LOD (simpler when zoomed out)

### Targets
- 60fps with 500 nodes
- Sub-100ms selection
- Smooth dragging

## Current Status

### Complete ✅
- 19 node types defined
- State machine implemented
- Action overlay system
- Placement flow working
- Basic styling complete

### In Progress 🔄
- Connection system (70%)
- Edge validation
- Multi-select
- Copy/paste

### Planned ⏳
- Node templates
- Custom types
- Auto-layout
- Search/filter
- Minimap

## Known Issues

1. Connection preview missing during drag
2. Performance drops at 500+ nodes
3. No persistence to disk
4. No undo/redo
5. Incomplete accessibility

## Usage Examples

### Create Node
```swift
let node = StoryNode.create(
    title: "Hero",
    type: .character,
    at: CGPoint(x: 100, y: 100)
)
nodeStore.addNode(node)
```

### Connect Nodes
```swift
let edge = StoryEdge(
    from: sourceNode.id,
    to: targetNode.id,
    type: .dialogue
)
edgeStore.addEdge(edge)
```

### Custom Styling
```swift
let style = NodeStyle(
    type: .character,
    mode: .expanded,
    isSelected: true,
    isHovered: false,
    isDragging: false,
    colorScheme: .dark
)
```