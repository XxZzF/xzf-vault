# Maven Overlays.md
[https://maven.apache.org/plugins/maven-war-plugin/overlays.html](https://maven.apache.org/plugins/maven-war-plugin/overlays.html) 讲的很明白。

**Maven Overlays在构建时合并多个web项目并打包成一个新的web项目。**

重点：

> Overlays are applied with a first-win strategy (hence if a file has been copied by one overlay, it won't be copied by another). Overlays are applied in the order in which they are defined in the `<overlays>` configuration. If no configuration is provided, the order in which the dependencies are defined in the POM is used (warning: this is not deterministic, especially if you have overlays as transitive dependencies). In case of a mixed situation (e.g. configured overlays and non-configured overlays), non-configured overlays are applied after configured overlays.

> 

> 默认情况下，使用“先赢策略”应用覆盖（因此，如果一个文件已经被一个Overlay复制，它将不会被另一个Overlay复制）。Overlay按照在 <overlays> 配置中定义的顺序应用。如果没有提供配置，则使用在 POM 中定义依赖关系的顺序（警告：这不是确定性的，特别是如果你有Overlay作为传递依赖项）。在混合情况下（例如，配置的Overlay和非配置的Overlay），非配置的Overlay在配置的Overlay之后应用。

> 

> By default, the source of the project (a.k.a the current build) is added first (e.g. before any overlay is applied). The current build is defined as a special overlay with no `groupId`, `artifactId`. If overlays need to be applied first, simply configure the current build after those overlays.

> 

> 默认情况下，项目的源代码（即当前构建）被首先添加（例如，在应用任何Overlay之前）。当前构建被定义为一个没有 groupId、artifactId 的特殊Overlay。如果需要先应用Overlay，请简单地在这些Overlay之后配置当前构建。

> 

> **没有在<overlays>元素中进行配置的WAR dependence将在<overlays>中的WAR之后被复制。**

