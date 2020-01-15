# Hash Versioning
Versioning is everywhere in tech and allows us to reference a point in time
for our software, hardware or anything in between.

Most use a simple dot separated list such as Python, JavaScript or NodeJS.
Typically referring to [SemVer](https://semver.org) for how the numbers are incremented.
Others use a single number like Firefox, Chrome or Safari.
The other common format is to just use the date, called [CalVer](https://calver.org),
such as Ubuntu or Unity.

Some like to add a little extra with a cool name like Android or Ubuntu.

The problem is that these formats assume a rate of releases to be rather slow.
That is, maybe once a month or twice a year, what if we release 2-10 times a day?

> Note:
    Most, if not all, of the projects listed above do have some sort of nightly
    or early access build that is published much much more frequently.
    But for comparing we are only concerned with those that reach production
    status.

## Scheme
HashVer format consists of 3 or 4 values separated by a period.

1. Full year (`printf("%Y")`)
1. Zero padded month  (`printf("%m")`)
1. [Optional] Zero padded day (`printf(""%d)`)
1. 6-10 characters of the current source controls commits hash
    1. This can be done using rev-parse and cut
    1. `git rev-parse HEAD | cut -c-8`

> A simple bash script to print a version is:
```bash
printf '%(%Y-%m-)T'; git rev-parse HEAD | cut -c-8
```
Note the 8 at the end is adjustable.
To include the day use `(%Y-%m-%d-)` inside the parenthesis.

> Warning:
    This does make the assumption you are using a source control that uses
    a hash to refer to the current commit or revision.
    Git for example does, Subversion does not.

Examples:

1. 2020.01.67092445a
1. 2019.07.21.3731a8be0f

## Advantages
Each version includes the date and hash commit, this gives us a few advantages
over existing versioning schemes.

1. Similar to CalVer we will know the month or optionally the exact day of the release
1. Unlike CalVer we will also know the exact commit this release is from
1. We can automatically calculate the version as part of our deployment process

## When to use HashVer
HashVer will not apply to every project use the following as a rough guide on
good use cases.

1. Do you deploy to production after most commits?
1. Are your changelog updates mostly a single change?
1. Do you have an automated process to deploy?
    * Full CI/CD recommended
1. Do you have safety checks in place for bad changes?
    * High code coverage
    * Health checks
    * Monitoring
    * Alerting
1. Can you rollback easily?
1. Do your users always use the latest version?

    To describe this a little more consider a website like facebook.
    Whenever they update and release a new version of the core website, you do
    not get an option to use the new version or stay on the old one.
    You use whatever version is sent to you.

If you meet all or most of these, you can use hash versioning.

## When not to use HashVer
There are many more reasons to not use hash versioning than there are reasons to use
it.
To name a few:

1. Does backwards compatability matter to you?
1. Does the user need to install or update manually?
    * Mobile apps probably will not work
    * PWA's will probably work
1. Do you release infrequently?
    * Long gaps in releases make hash versioning irrelevant use calver or semver
1. Do you support more than one release at a time?
1. Do you have a suite of external checks you must go through to release?
    * This just slows you down at which point you will need to batch releases anyway
1. Is your project used as a dependency to other projects?
1. Do you have to manually deploy?
1. Do you have to manually test?
1. Do you need explicit approval from someone that takes a long time?

Ok, maybe more than a few but you get the point.

## Hash Version Versioning
Hash version specification does not fit the use case for hash version and as such
it instead uses semantic versioning.

Hash version is also in the early stages and as such is tagged with draft.

Current version is 0.1.0-draft
