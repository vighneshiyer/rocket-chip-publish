# rocket-chip-publish

## Overview
[rocket-chip](https://github.com/chipsalliance/rocket-chip/) is a Chisel library that holds the Rocket RISC-V core, TileLink/AXI4 bus peripherals, L1 caches, and supporting infrastructure to build an SoC.
Using rocket-chip in a containing project (such as [Chipyard](https://github.com/ucb-bar/chipyard/)) usually entails cloning rocket-chip as a submodule and using it as a source dependency.
This can be undesirable in cases where you just want to use components of rocket-chip and unwieldy since rocket-chip contains 100s of Scala sources to compile.

This repo contains a simple publishing script and Github Actions workflow to publish rocket-chip to Sonatype snapshots.
It runs on an hourly timer, looks for new commits to rocket-chip, and publishes a new version to Sonatype when an update to rocket-chip/master is seen.

The published snapshots are enumerated here [snapshots:edu/berkeley/cs/rocketchip_2.12](https://oss.sonatype.org/content/repositories/snapshots/edu/berkeley/cs/rocketchip_2.12/)
The version of each snapshot is `1`.`chisel major version used in rocket-chip`-`git hash of rocket-chip commit`-SNAPSHOT.

To use a snapshot, add this snippet to your `build.sbt`:
```sbt
resolvers ++= Seq(
    Resolver.sonatypeRepo("snapshots"),
    Resolver.sonatypeRepo("releases"),
    Resolver.mavenLocal
)

libraryDependencies ++= Seq("edu.berkeley.cs" %% "rocketchip" % "1.5-b503f8ac2-SNAPSHOT")
```

- Why publish snapshots instead of versioned releases?
    - The user-facing API of rocket-chip isn't stable and versioned releases require a stable API surface. The minor version of the snapshot releases track the minor Chisel version that is used by rocket-chip. Each snapshot is published once and will not be overwritten.
- Why is the publishing scripting sitting outside rocket-chip?
    - It may be integrated into rocket-chip once proven useful.
- How are rocket-chip's subprojects (hardfloat, api-config-chipsalliance, rocket-macros) versioned?
    - They are published as snapshots with the same version as rocket-chip. Each rocket-chip snapshot uses published snapshot versions of the subprojects (with the exact same version string). [See this .pom](https://oss.sonatype.org/content/repositories/snapshots/edu/berkeley/cs/rocketchip_2.12/1.5-0d0fa9001-SNAPSHOT/rocketchip_2.12-1.5-0d0fa9001-SNAPSHOT.pom) as an example.
