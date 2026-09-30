# Fork changelog

Changes made in this fork of [wagiro/BurpBounty](https://github.com/wagiro/BurpBounty), which has
had no commits since March 2023. Upstream history and authorship are preserved and the Apache-2.0
licence is unchanged.

## Unreleased

### Fixed

- **The project did not build at all on a current Gradle.** `gradle build` failed while still
  evaluating the build script:

  ```
  Could not find method compile() for arguments
  [net.portswigger.burp.extender:burp-extender-api:1.7.13]
  ```

  `compile` was deprecated in Gradle 4.10 and removed in Gradle 7, so the build stopped before
  reaching a single source file. The dependencies now use `implementation`.

- **The source directory pointed somewhere that does not exist.** The build declared
  `srcDir 'src'`, but this repository keeps its 60 Java files under `main/java` and its resources
  under `main/resources`; there is no `src` directory. The source set now names the real
  directories, so the sources are actually compiled.

- **The `fatJar` task used APIs removed in Gradle 7+.** `baseName` became `archiveBaseName`, and
  `configurations.compile` no longer exists; the task now resolves the runtime classpath and
  bundles gson, which the extension needs at runtime. A duplicates strategy is set, without which
  Gradle 7 fails the task on the metadata entries that several dependencies share.

### Verified build

`gradle fatJar` succeeds against Gradle 7.4 on JDK 17, producing
`build/libs/scan-check-builder-all.jar` (545 KB, 369 entries) containing `burp/BurpExtender.class`,
the `burpbountyfree` classes and the bundled gson runtime — a jar Burp can load directly.

### Added

- A `.gitignore`. The repository had none, so every build left `build/` and `.gradle/` as
  untracked noise.
