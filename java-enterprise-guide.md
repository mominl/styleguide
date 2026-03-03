# Using the Google Java Style Guide in Enterprise Projects

This document describes how enterprise teams can adopt the
[Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)
and integrate its tooling into their development workflows.

## IDE Formatter Configuration

This repository provides ready-to-use formatter configurations for two major
Java IDEs. Importing these ensures that all developers on a team produce
consistently formatted code.

### Eclipse

1. Download
   [`eclipse-java-google-style.xml`](https://github.com/google/styleguide/blob/gh-pages/eclipse-java-google-style.xml).
2. In Eclipse, go to **Window → Preferences → Java → Code Style → Formatter**.
3. Click **Import…** and select the downloaded XML file.
4. The **GoogleStyle** profile will appear in the "Active profile" dropdown.
   Select it and click **Apply and Close**.
5. To auto-format a file, press <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>F</kbd>
   (or <kbd>Cmd</kbd>+<kbd>Shift</kbd>+<kbd>F</kbd> on macOS).

### IntelliJ IDEA

1. Download
   [`intellij-java-google-style.xml`](https://github.com/google/styleguide/blob/gh-pages/intellij-java-google-style.xml).
2. In IntelliJ IDEA, go to **File → Settings → Editor → Code Style → Java**
   (or **IntelliJ IDEA → Preferences** on macOS).
3. Click the gear icon next to the "Scheme" dropdown and select
   **Import Scheme → IntelliJ IDEA code style XML**.
4. Select the downloaded XML file and click **OK**.
5. Choose the **GoogleStyle** scheme and click **Apply**.
6. To auto-format a file, press <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>L</kbd>
   (or <kbd>Cmd</kbd>+<kbd>Option</kbd>+<kbd>L</kbd> on macOS).

## Command-Line Formatting with google-java-format

[google-java-format](https://github.com/google/google-java-format) is an
open-source tool that reformats Java source code to comply with the Google Java
Style Guide. It can be run from the command line:

```sh
# Format a file in place
java -jar google-java-format-*.jar --replace MyFile.java

# Check formatting without modifying files (useful in CI)
java -jar google-java-format-*.jar --dry-run --set-exit-if-changed MyFile.java
```

## Build Tool Integration

### Maven

Add the
[fmt-maven-plugin](https://github.com/spotify/fmt-maven-plugin) to your
`pom.xml` to check or apply Google Java Style formatting during the build:

```xml
<plugin>
  <groupId>com.spotify.fmt</groupId>
  <artifactId>fmt-maven-plugin</artifactId>
  <version>2.25</version>
  <executions>
    <execution>
      <goals>
        <goal>check</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

Run `mvn fmt:check` to verify formatting or `mvn fmt:format` to auto-format.

### Gradle

Use the
[spotless](https://github.com/diffplug/spotless/tree/main/plugin-gradle)
plugin:

```groovy
plugins {
  id 'com.diffplug.spotless' version '7.0.2'
}

spotless {
  java {
    googleJavaFormat()
  }
}
```

Run `./gradlew spotlessCheck` to verify or `./gradlew spotlessApply` to fix
formatting.

## CI/CD Enforcement

Adding a formatting check to your continuous integration pipeline prevents
style violations from reaching the main branch. A minimal GitHub Actions
example:

```yaml
name: Java Style Check
on: [pull_request]
jobs:
  format-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17
      - name: Check formatting
        uses: axel-op/googlejavaformat-action@v3
        with:
          args: "--dry-run --set-exit-if-changed"
```

## Recommended Adoption Steps

1. **Agree on the style** – share the
   [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)
   with your team and confirm adoption.
2. **Distribute IDE settings** – commit the Eclipse or IntelliJ formatter XML
   to your project repository (e.g., in a `.ide/` or `config/` directory) so
   every developer uses the same profile.
3. **Add a build plugin** – integrate `fmt-maven-plugin` or `spotless` so
   formatting is verified on every build.
4. **Enforce in CI** – add a formatting check step to your CI pipeline to catch
   violations before merge.
5. **Format existing code** – run the formatter across the entire codebase in a
   single commit to establish a clean baseline.
