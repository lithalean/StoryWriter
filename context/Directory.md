lithalean@Mac ~ % cd /Users/lithalean/Documents/Developer/3_Build/Xcode/StoryWriter
lithalean@Mac StoryWriter % tree
.
├── StoryWriter
│   ├── App
│   │   ├── AppBackground.swift
│   │   ├── Assets.xcassets
│   │   │   ├── AccentColor.colorset
│   │   │   │   └── Contents.json
│   │   │   ├── AppIcon.appiconset
│   │   │   │   └── Contents.json
│   │   │   └── Contents.json
│   │   ├── ContentView.swift
│   │   ├── Item.swift
│   │   └── StoryWriterApp.swift
│   ├── Core
│   │   ├── CanvasBackground.swift
│   │   ├── FileBrowser.swift
│   │   ├── FileSystemModel.swift
│   │   ├── MarkdownReader.swift
│   │   └── MarkdownWriter.swift
│   ├── Formatter
│   │   ├── FormatControlsView.swift
│   │   └── FormatterPanel.swift
│   ├── Info.plist
│   ├── Inspector
│   │   ├── InspectorPanel.swift
│   │   ├── InspectorView.swift
│   │   └── OutlinerView.swift
│   ├── Location
│   │   └── LocationSheet.swift
│   ├── Managers
│   │   ├── ProjectManager.swift
│   │   └── StatefulPreviewWrapper.swift
│   ├── MarkdownUI
│   │   ├── DSL
│   │   │   ├── Blocks
│   │   │   │   ├── Blockquote.swift
│   │   │   │   ├── BulletedList.swift
│   │   │   │   ├── CodeBlock.swift
│   │   │   │   ├── Heading.swift
│   │   │   │   ├── ListContentBuilder.swift
│   │   │   │   ├── ListItem.swift
│   │   │   │   ├── MarkdownContent.swift
│   │   │   │   ├── MarkdownContentBuilder.swift
│   │   │   │   ├── NumberedList.swift
│   │   │   │   ├── Paragraph.swift
│   │   │   │   ├── TaskList.swift
│   │   │   │   ├── TaskListContentBuilder.swift
│   │   │   │   ├── TaskListItem.swift
│   │   │   │   ├── TextTable.swift
│   │   │   │   ├── TextTableColumn.swift
│   │   │   │   ├── TextTableColumnAlignment.swift
│   │   │   │   ├── TextTableColumnBuilder.swift
│   │   │   │   ├── TextTableRow.swift
│   │   │   │   ├── TextTableRowBuilder.swift
│   │   │   │   └── ThematicBreak.swift
│   │   │   └── Inlines
│   │   │       ├── Code.swift
│   │   │       ├── Emphasis.swift
│   │   │       ├── InlineContent.swift
│   │   │       ├── InlineContentBuilder.swift
│   │   │       ├── InlineImage.swift
│   │   │       ├── InlineLink.swift
│   │   │       ├── LineBreak.swift
│   │   │       ├── SoftBreak.swift
│   │   │       ├── Strikethrough.swift
│   │   │       └── Strong.swift
│   │   ├── Extensibility
│   │   │   ├── AssetImageProvider.swift
│   │   │   ├── AssetInlineImageProvider.swift
│   │   │   ├── CodeSyntaxHighlighter.swift
│   │   │   ├── DefaultImageProvider.swift
│   │   │   ├── DefaultInlineImageProvider.swift
│   │   │   ├── ImageProvider.swift
│   │   │   └── InlineImageProvider.swift
│   │   ├── Parser
│   │   │   ├── BlockNode.swift
│   │   │   ├── BlockNode+Rewrite.swift
│   │   │   ├── HTMLTag.swift
│   │   │   ├── InlineNode.swift
│   │   │   ├── InlineNode+Collect.swift
│   │   │   ├── InlineNode+Rewrite.swift
│   │   │   └── MarkdownParser.swift
│   │   ├── Renderer
│   │   │   ├── AttributedStringInlineRenderer.swift
│   │   │   ├── InlineTextStyles.swift
│   │   │   └── TextInlineRenderer.swift
│   │   ├── Theme
│   │   │   ├── BlockStyle
│   │   │   │   ├── BlockConfiguration.swift
│   │   │   │   ├── BlockStyle.swift
│   │   │   │   ├── CodeBlockConfiguration.swift
│   │   │   │   ├── ListBullet.swift
│   │   │   │   ├── ListMarkerConfiguration.swift
│   │   │   │   ├── TableBackgroundStyle.swift
│   │   │   │   ├── TableBorderStyle.swift
│   │   │   │   ├── TableCellConfiguration.swift
│   │   │   │   └── TaskListMarkerConfiguration.swift
│   │   │   ├── TextStyle
│   │   │   │   ├── Styles
│   │   │   │   │   ├── BackgroundColor.swift
│   │   │   │   │   ├── EmptyTextStyle.swift
│   │   │   │   │   ├── Font+FontProperties.swift
│   │   │   │   │   ├── FontCapsVariant.swift
│   │   │   │   │   ├── FontDigitVariant.swift
│   │   │   │   │   ├── FontFamily.swift
│   │   │   │   │   ├── FontFamilyVariant.swift
│   │   │   │   │   ├── FontProperties.swift
│   │   │   │   │   ├── FontPropertiesAttribute.swift
│   │   │   │   │   ├── FontSize.swift
│   │   │   │   │   ├── FontStyle.swift
│   │   │   │   │   ├── FontWeight.swift
│   │   │   │   │   ├── FontWidth.swift
│   │   │   │   │   ├── ForegroundColor.swift
│   │   │   │   │   ├── StrikethroughStyle.swift
│   │   │   │   │   ├── TextKerning.swift
│   │   │   │   │   ├── TextTracking.swift
│   │   │   │   │   └── UnderlineStyle.swift
│   │   │   │   ├── TextStyle.swift
│   │   │   │   └── TextStyleBuilder.swift
│   │   │   └── Theme.swift
│   │   ├── Utility
│   │   │   ├── BlockNode+ColorSchemeImage.swift
│   │   │   ├── Color+RGBA.swift
│   │   │   ├── Deprecations.swift
│   │   │   ├── FlowLayout.swift
│   │   │   ├── Indexed.swift
│   │   │   ├── InlineNode+PlainText.swift
│   │   │   ├── InlineNode+RawImageData.swift
│   │   │   ├── Int+Roman.swift
│   │   │   ├── RelativeSize.swift
│   │   │   ├── ResizeToFit.swift
│   │   │   └── String+KebabCase.swift
│   │   └── Views
│   │       ├── Blocks
│   │       │   ├── BlockMargin.swift
│   │       │   ├── BlockNode+View.swift
│   │       │   ├── BlockquoteView.swift
│   │       │   ├── BlockSequence.swift
│   │       │   ├── BulletedListView.swift
│   │       │   ├── CodeBlockView.swift
│   │       │   ├── ColumnWidthPreference.swift
│   │       │   ├── HeadingView.swift
│   │       │   ├── ImageFlow.swift
│   │       │   ├── ListItemSequence.swift
│   │       │   ├── ListItemView.swift
│   │       │   ├── NumberedListView.swift
│   │       │   ├── ParagraphView.swift
│   │       │   ├── TableBackgroundView.swift
│   │       │   ├── TableBorderSelector.swift
│   │       │   ├── TableBorderView.swift
│   │       │   ├── TableBounds.swift
│   │       │   ├── TableCell.swift
│   │       │   ├── TableView.swift
│   │       │   ├── TaskListItemView.swift
│   │       │   ├── TaskListView.swift
│   │       │   ├── TextStyleAttributesReader.swift
│   │       │   └── ThematicBreakView.swift
│   │       ├── Environment
│   │       │   ├── Environment+BaseURL.swift
│   │       │   ├── Environment+CodeSyntaxHighlighter.swift
│   │       │   ├── Environment+ImageProvider.swift
│   │       │   ├── Environment+InlineImageProvider.swift
│   │       │   ├── Environment+List.swift
│   │       │   ├── Environment+SoftBreakMode.swift
│   │       │   ├── Environment+Table.swift
│   │       │   ├── Environment+TextStyle.swift
│   │       │   └── Environment+Theme.swift
│   │       ├── Inlines
│   │       │   ├── ImageView.swift
│   │       │   └── InlineText.swift
│   │       └── Markdown.swift
│   ├── Project
│   │   ├── ProjectBrowser.swift
│   │   ├── ProjectState.swift
│   │   ├── ProjectStatusbar.swift
│   │   ├── ProjectStatusbarConfig.swift
│   │   ├── ProjectToolbar.swift
│   │   └── ProjectToolbarConfig.swift
│   ├── Reader
│   │   ├── ReaderState.swift
│   │   └── ReaderWindow.swift
│   ├── Sources
│   │   └── Shared
│   │       ├── Extensions
│   │       │   ├── FilePermissionsHelper.swift
│   │       │   └── HapticFeedback.swift
│   │       ├── GlassButtonStyle.swift
│   │       ├── PlatformColor.swift
│   │       └── ToolbarStyle.swift
│   ├── Views
│   │   ├── Chapter
│   │   ├── Character
│   │   │   └── CharacterSheet.swift
│   │   └── Index
│   │       ├── IndexCard.swift
│   │       └── IndexWindow.swift
│   └── Writer
│       ├── WriterDocument.swift
│       ├── WriterState.swift
│       └── WriterWindow.swift
└── StoryWriter.xcodeproj
    ├── Info.plist
    ├── project.pbxproj
    ├── project.xcworkspace
    │   ├── contents.xcworkspacedata
    │   ├── xcshareddata
    │   │   └── swiftpm
    │   │       └── configuration
    │   └── xcuserdata
    │       └── lithalean.xcuserdatad
    │           └── UserInterfaceState.xcuserstate
    └── xcuserdata
        └── lithalean.xcuserdatad
            └── xcschemes
                └── xcschememanagement.plist

47 directories, 168 files
lithalean@Mac StoryWriter % 