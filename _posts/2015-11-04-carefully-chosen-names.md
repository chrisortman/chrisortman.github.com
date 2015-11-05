---
layout: post
title: "Carefully chosen names"
comments: true
tags:
 - code
---

# Carefully chosen names

I want to illustrate the importance of a carefully chosen name and how big of a difference it can make.

I was putting together a screen the other day and needed to conditionally show a button. The button should only be shown if the user is admin, or if when the user clicks the button we can show them an 'edit' screen for it. Whether or not we can show them an edit screen depends on if we have implemented the edit feature for this particular record type.

Take a look at some of the possible choices for how this if statement can look

```

// option 1
if ( user.hasRole('admin') && isSpecialType() ) {
// render
}

// option 2
if ( user.hasRole('admin') && shouldShowButton() ) {
// render
}

// option 3
if ( user.hasRole('admin') && weHaveImplementedEditForThisType ) {
// render
}

```

Now, imagine the developer who implements this leaves mid project. And *you* have to come
in and make a change months later.

With option 1 or 2 you might think there is some business rule related to the bahaviour.

Also think about the reasons that the method will change. The method will change as edit gets implemented for these other types. So the change you are making is tied to the name. If it is option 1 or 2 then you don't quite know
