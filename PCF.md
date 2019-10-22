# [Architecture Guideline] : /src Folder

  

A major part of this architecture: the feature folder, consists of a module for each of the application’s feature.

Let’s examining the `/explore/` module in more details:

*/actions*

Like in most react/react-native application. this folder contains the Action Creators for this feature.

*/components*

Here we place the explore feature’s components and their related styles

*/containers*

The feature’s redux-related logic is placed here. For this use-case there’s a single container(representing a screen) being exported, so we placed it in the index file.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwNDE3ODg2NjgsODI3MTUyNTcxLC0zMD
c5NTkxNjldfQ==
-->