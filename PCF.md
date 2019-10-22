# [Architecture Guideline] : /src Folder

  

A major part of this architecture: the feature folder, consists of a module for each of the application’s feature.

Let’s examining the `/explore/` module in more details:

*/actions*

Like in most react/react-native application. this folder contains the Action Creators for this feature.

*/components*

Here we place the explore feature’s components and their related styles

*/containers*

The feature’s redux-related logic is placed here. For this use-case there’s a single container(representing a screen) being exported, so we placed it in the index file.

Each feature has a reducer that mutates its own slice of the application state. All reducers are later combined using redux’s  `combineReducers`  function.

*/selectors*

This might come across as a bit strange to some of us, however this segment of our architecture is influenced by the  [reselect package](https://www.npmjs.com/package/reselect), which enables us to efficiently compute derived data from our application’s state.

In this architecture, we grouped the application selectors on a feature-basis so as to make interaction between various aspects of the app easy to reason about. More details on how reselect works can be found  [here](https://www.npmjs.com/package/reselect).

_/constants.js_

This file contains static values used within the feature. An example of what we could store here is  `ACTION_TYPES`  data.

This is another component of our architecture slightly influenced by a  [react-package](https://www.npmjs.com/package/react-navigation)

Due to the scale of this application, we wouldn’t be relying on the out-of-the-box navigation with  `this.props.navigation`, rather we would be be connecting the app’s navigation to the overall application state. This means that our redux-backed store would be aware of the navigation state.

Lets explore the roles of the various directories in this module.

_/actions_

This contains logic for a bunch of navigation-specific action-creators

_/containers_

This is the where we connect our navigation logic to the application state — using  `**mapStateToProps**`  and  `**mapDispatchToProps**`
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTMyODAzMjQ5Nyw4MjcxNTI1NzEsLTMwNz
k1OTE2OV19
-->