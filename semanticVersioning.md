# Semantic Versioning

## Why semantic versioning? 

Semantic Versioning 2.0.0 is a way to name different versions of a program or game so that people know which version they have and if there is a newer version available.

Imagine you have a toy box and you put different toys in it. When you first make the toy box, you put a few toys in it and call it version 1.0.0. Later, you add some new toys to the box, but they don't change the toys that were already in there, so you call it version 1.1.0. And then, you fix a toy that was broken, and you call it version 1.1.1.

If you change a toy in a way that makes it different from the original toy and it won't work with the old way, you have to call it a new version, for example, version 2.0.0.

That's what Semantic Versioning 2.0.0 does for programs and games, it helps developers and users to know what has changed in each version and decide if they want to download the new version or not.

## Is this a standard?

Yes, Semantic Versioning 2.0.0 (SemVer) is a widely-used standard for versioning software releases. It is a widely adopted standard in many programming languages and projects, and it's becoming a de facto standard for versioning software releases. The standard is used to communicate what has changed in each release and make it easy for users to understand the impact of an update and decide whether to install it.

It's also supported by many package managers, build tools, and other software development tools. So, it's considered a widely accepted standard for versioning software releases.

## Numbering System in Semver

  The numbering system looks like `MAJOR.MINOR.PATCH`  `7.0.0` 

### when does this number change?

If there is a new bug fix then the **patch** version is gonna go up by one number.  eg: `7.0.1`
If there is one new feature or two or three new features that came out then also the **minor** version goes up by one number. eg `7.2.0`

If you have a breaking change then the Major version goes up by one number. eg `8.0.0` 
Try to keep the breaking changes to the minimum if you have a new way to do a thing try to add it in the minor change and then flag the old feature as deprecated and notify them that we will remove this in the future.

If you see something like this `^8.0.0` this means that it will never bump past the major  changes
only the minor and the patch are allowed to update.

If you see something like this `~8.0.0` this means that only patches are allowed to be updated.


## What are alpha, Beta, and rc-1 in semver?

In Semantic Versioning 2.0.0 (SemVer), "alpha", "beta", and other similar terms are used as pre-release metadata. They indicate that the version is not a stable release, and is intended for testing or for a specific group of users.

"Beta" typically indicates that the software is approaching a stable release, but may still contain bugs or unfinished features. It's usually used for external testing or for a larger group of testers.

Other pre-release labels like "RC" (Release Candidate) or "RC1" (Release candidate 1) are used to indicate that the software is almost ready for a stable release, and it's intended for testing before the final release.

These labels are used to indicate that the software may not be suitable for production use and that it's intended for testing only. They allow developers to communicate the status of a release to users and make it clear that the version may not be stable.
