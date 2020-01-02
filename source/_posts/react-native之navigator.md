---
title: Navigator is deprecated and has been removed from this package.
date: 2017-05-09
tags: javascript
---

**React-Native** 升级到0.44.0版本后，引用了

    import { Navigator} from 'react-native'; 

会报错：
**Navigator is deprecated and has been removed from this package.**
<!--more-->
通过查阅**react-native**中文网得知：
从0.44版本开始，**Navigator**被从**react native**的核心组件库中剥离到了一个名为**react-native-deprecated-custom-components**的单独模块中。如果你需要继续使用**Navigator**，则需要先

	npm i -S react-native-deprecated-custom-components

然后从这个模块中import，即

	import { Navigator } from 'react-native-deprecated-custom-components'

然后就可以像以前那样使用**Navigator**组件了。