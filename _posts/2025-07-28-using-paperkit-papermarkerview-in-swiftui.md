---
title: Using PaperKit PaperMarkerView in SwiftUI
date: 2025-07-28 00:00:00 +0000
categories: [Apple, SwiftUI, WWDC]
---

I've had an iPad app idea rattling around in my head for a while which would leverage pencil kit and a drawing canvas to enable a specific scenario that I think might form the basis of an app. I particularly like Freeform and so my mental model was to use something like that approach. Imagine my joy when I saw the session announcing PaperKit at WWDC (https://developer.apple.com/videos/play/wwdc2025/285).

![PaperKit WWDC Session](/assets/img/paperkit/paperkit_session_image.jpeg)

I watched this session with great anticipation and delight at the capabilities that are clearly directly used in Freeform, but was a little saddened to see that there was no SwiftUI wrapper. Because I wanted to start prototyping the app idea, I decided to knock up a quick PoC for leveraging PaperKit in SwiftUI and I thought it might be helpful to share that. It's definitely not comprehensive or production ready but gets you up and running to play around.

## Architecture Overview

The integration between SwiftUI and PaperKit requires several layers to bridge the two frameworks effectively. Here's how the various components relate to each other:

```mermaid
graph TB
    %% SwiftUI Layer
    subgraph "SwiftUI Layer"
        ContentView["ContentView<br/>@State coordinator<br/>@State isEditMode"]
        PaperMarkupView["PaperMarkupView<br/>UIViewControllerRepresentable<br/>canvasSize, isEditable"]
    end
    
    %% Bridge Layer  
    subgraph "Bridge Layer"
        Coordinator["PaperMarkupCoordinator<br/>@Observable<br/>weak wrapperViewController"]
    end
    
    %% UIKit Layer
    subgraph "UIKit Layer"
        WrapperVC["PaperMarkupWrapperViewController<br/>UIViewController<br/>PaperMarkupViewController.Delegate"]
    end
    
    %% Framework Layer
    subgraph "Apple Frameworks"
        PaperVC["PaperMarkupViewController<br/>(PaperKit)"]
        ToolPicker["PKToolPicker<br/>(PencilKit)"]
        MarkupModel["PaperMarkup<br/>(PaperKit Data Model)"]
    end
    
    %% Storage Layer
    subgraph "Storage Layer"
        FileSystem["File System<br/>~/Library/Application Support/<br/>[Bundle ID]/markup.data"]
    end
    
    %% Ownership Relationships (Solid Lines)
    ContentView --> PaperMarkupView
    ContentView --> Coordinator
    PaperMarkupView --> Coordinator
    PaperMarkupView --> WrapperVC
    WrapperVC --> PaperVC
    WrapperVC --> ToolPicker  
    WrapperVC --> MarkupModel
    
    %% Weak References (Dashed Lines)
    Coordinator -.->|weak ref| WrapperVC
    WrapperVC -.->|weak ref| Coordinator
    
    %% Protocol Conformances (Dotted Lines)
    PaperMarkupView -.->|conforms to| UIViewControllerRepresentable["UIViewControllerRepresentable<br/>(SwiftUI Protocol)"]
    WrapperVC -.->|conforms to| Delegate["PaperMarkupViewController.Delegate<br/>(PaperKit Protocol)"]
    Coordinator -.->|conforms to| Observable["@Observable<br/>(Swift Macro)"]
    
    %% Data Flow (Bold Lines)
    WrapperVC ==>|auto-save| FileSystem
    FileSystem ==>|auto-load| WrapperVC
    
    %% Styling
    classDef swiftui fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    classDef bridge fill:#f3e5f5,stroke:#4a148c,stroke-width:2px  
    classDef uikit fill:#e8f5e8,stroke:#1b5e20,stroke-width:2px
    classDef framework fill:#fff3e0,stroke:#e65100,stroke-width:2px
    classDef storage fill:#fce4ec,stroke:#880e4f,stroke-width:2px
    classDef protocol fill:#f5f5f5,stroke:#424242,stroke-width:1px,stroke-dasharray: 5 5
    
    class ContentView,PaperMarkupView swiftui
    class Coordinator bridge
    class WrapperVC uikit
    class PaperVC,ToolPicker,MarkupModel framework
    class FileSystem storage
    class UIViewControllerRepresentable,Delegate,Observable protocol
```

## Implementation

Here's the complete implementation of the SwiftUI wrapper for PaperKit:

{% gist clarkezone/68eb3ee13b5607782ceb2e20cece4ab3 %}