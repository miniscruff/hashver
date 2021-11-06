# Hash Versioning
Versioning is everywhere in tech and allows us to reference a point in time
for our software, hardware or anything in between.

Most use a simple dot separated list such as Python, JavaScript or NodeJS.
Typically referring to [Semantic Versioning](https://semver.org) for how the numbers are incremented.
Others use a single number like Firefox, Chrome or Safari.
The other common format is to just use the date, called [Calendar Versioning](https://calver.org),
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
1. [Optional] Zero padded day (`printf("%d")`)
1. 10+ characters of the current source controls commits hash

Examples:

1. 2020.01.67092445a1abc
1. 2019.07.21.3731a8be0f1a8

> Note
    If you are using existing tools that rely on semver formatting, you can instead
    use a `+` between the day and hash.
    This specifies the hash as an optional metadata to semver checks.

## Ordering
Because commit hashes are random the ordering is not guaranteed when versions
collide on the same day or month if not including days.
You should include enough granularity so you do not release more often than
the accuracy allows.
If you release many times a day, including more parts of the time like hours
can work.
Or you can include some other metadata like CI build index to make the builds
sortable.

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

1. Are backwards incompatable changes optional?
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

## Generating Hash Versions
As part of automated CI/CD solutions you will want to generate your hash version.

1. [hashver-python](https://github.com/miniscruff/hashversion-python) can generate versions and handles more.
1. Bash: `printf '%(%Y-%m-)T'; git rev-parse --short=12 HEAD`
1. Bash with day: `printf '%(%Y-%m-%d-)T'; git rev-parse --short=12 HEAD`
1. Bash with semver format: `printf '%(%Y-%m-%d+)T'; git rev-parse --short=12 HEAD`

## Related Practices
As part of your deployment pipeline when using hash versioning, here are some related practices.
These are not directly part of hash version but are related enough to mention.

### Documentation as Code
1. As you break down the barriers of releasing services, you will run into the problem of documentation
1. If you write your documentation alongside your service, than you can deploy both in parallel
1. Each change along with documentation updates go together in one commit, preferably as a pull request
1. There will be no more time to make wiki or PDF changes before a change is rolled out

### Automated Changelog Updates
1. Keeping track of every change will be very hard if they are released and deployed frequently
1. Instead opt for an automated approach to changelog generation
1. Specifications like [keep a changelog](https://keepachangelog.com/) are a good place to start
1. [hashver-python](https://github.com/miniscruff/hashversion-python) packages changelog management together
1. [changie](https://github.com/miniscruff/changie) also supports hash versioning
1. Changelogs should be packaged or referenced in your documentation as well

### Single Environment Deploys
1. As you deploy faster and faster you may consider going from master straight to production
1. With a strong CI/CD tool chain hash versioning keeps up with the speed of releases
1. If one environment feels too risky consider two with a 24 hour rollover window

### Feature Management
1. As you deploy to a single environment, or quickly through two, managing beta or early access features changes
1. Instead of holding new features out until they are "perfect" consider alternatives
1. Such as, just releasing it and not announcing it, depending on your circumstance this may work
1. Local feature flags that can be enabled for some users but not all
1. A feature flag service to modify and toggle flags based on many factors

## Hash Version Versioning
Hash version is in the early stages, all feedback is appreciated.
