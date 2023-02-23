---
author: Patrik Kaura
pubDatetime: 2023-02-23T22:02:00Z
title: Documenting system architecture with C4 diagrams
postSlug: documenting-system-architecture-with-c4-diagrams
featured: false
draft: false
tags:
  - system-design
ogImage: ""
description: Simple way to document your system architecture.
---

## Description

The C4 model is a software architecture diagramming approach that prioritizes abstractions
reflecting how software architects and developers think about and build software.
It takes an "abstraction-first" approach to create clear and concise diagrams of software
architecture.

C4 model structures diagrams into 4 levels of abstraction:

- `L1 - system context diagram`
- `L2 - container diagram`
- `L3 - component diagram`
- `L4 - code`

## C4 model parts

The C4 model provides a method for visualizing the static structures of a software
system through the use of containers, components, code, and people.
People are the human users of the software system.

- The software `system` is the highest
  level of abstraction, representing something that delivers value to its users, such as Sklik.
- `Containers` represent applications or data stores that must be operational for the entire
  software system to function, examples include serverside web apps, MySQL, Redis, or
  shell scripts.
- `Components` group related functionalities together and are encapsulated
  behind a well-defined interface.

### L1 - system context diagram

To document and visualize a software system effectively, a System Context diagram can serve as a great starting point. This type of diagram provides a holistic view of the system, with the system as a central box and its users and other interacting systems surrounding it. The `emphasis should be on people`, such as actors, roles, and personas, as well as software systems, rather than low-level technical details like protocols or technologies. The System Context diagram should be `simple enough to be presented to non-technical individuals`.

![L1 system](/assets/c4/l1-system.png)

> -- <cite>Simon Brown https://c4model.com</cite>

### L2 - container diagram

After gaining an understanding of how a software system operates within the larger IT environment, the next step is to create a Container diagram that zooms in on the system boundary. A container refers to a `separately deployable unit`, such as a server-side web application, single-page application, desktop application, mobile app, database schema, or file system, that executes code or stores data.

The Container diagram provides a `high-level overview` of the software architecture and illustrates how `responsibilities are distributed` across the containers. It also shows the major technology choices made and how the containers communicate with one another. This diagram is a simple, technology-focused representation that is beneficial for both software developers and support/operations staff.

![L2 container](/assets/c4/l2-container.png)

> -- <cite>Simon Brown https://c4model.com</cite>

### L3 - component diagram

After creating the Container diagram, the next step is to further analyze each container and decompose it into its `major structural building blocks` and their `interactions`. The Component diagram provides an overview of how a container is comprised of numerous "components", identifying each component, their individual `responsibilities`, and the `technology` and `implementation` details associated with them.

![L3 component](/assets/c4/l3-component.png)

> -- <cite>Simon Brown https://c4model.com</cite>

### L4 - code

The final step involves `zooming in` on each component to show how it is `implemented as code`, typically through the use of `UML class diagrams or entity relationship diagrams`. While this level of detail is optional and often available on-demand through tooling such as IDEs, it can be beneficial for explaining complex components.

If creating this diagram, it's ideal to `generate it automatically` using tooling such as an IDE or UML modelling tool. Only attributes and methods relevant to the story being told should be shown, as excessive detail is not recommended for anything but the most important or complex components.

![L4 code](/assets/c4/l4-code.png)

> -- <cite>Simon Brown https://c4model.com</cite>

# Usage

Personally, I prefer to use the C4-PlantUML plugin, which is described on Github as follows.

> C4-PlantUML combines the benefits of PlantUML and the C4 model for providing a simple way of describing and communicate software architectures – especially during up-front design sessions – with an intuitive language using open source and platform independent tools.

> -- <cite>Github https://github.com/plantuml-stdlib/C4-PlantUML</cite>

### Example

```python
@startuml

!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

Person(user, "user label", "description")
Person_Ext(user_ext_alias, "user external label", "description")

Container(container_alias, "container label", "technology", "description")
ContainerDb(container_db_alias, "container db label", "technology", "description")
ContainerQueue(container_queue_alias, "container queue label", "technology", "description")
Container_Ext(container_ext_alias, "container external label", "technology", "description")

Component(component_alias, "component label", "technology", "description")
ComponentDb(component_db_alias, "component db label", "technology", "description")
ComponentQueue(component_queue_alias, "component queue label", "technology", "description")
Component_Ext(component_ext_alias, "component external label", "technology", "description")

SHOW_LEGEND()
@enduml
```
