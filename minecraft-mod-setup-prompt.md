**Minecraft version:** _(optional, such as 26.3 or 1.21.1. Leave blank for the newest one NeoForge supports.)_

Set up a working NeoForge mod development environment for Minecraft Java Edition on this computer, with IntelliJ IDEA as the editor. Build a hello-world mod, prove that it loads in the development client, and open the project in IntelliJ ready to run. Do the whole thing yourself, straight through, and post short status lines as you go. Only stop to ask me something if you truly can't continue, or before you do anything that needs an administrator password.

**Ground rules:**

- Use **NeoForge** only, with its **ModDevGradle** template.
- Stay on this task. Don't install anything beyond the JDK and IntelliJ IDEA (no other launchers, IDEs, IntelliJ plugins, or tools unless I ask), and don't suggest unrelated services or purchases.
- Prefer installs that don't need admin rights. Never run `sudo` or an elevated Windows prompt without asking me first and saying why.
- Never ask me for passwords, a Minecraft, Microsoft or JetBrains login, or payment details.
- Only download from the official sources named below.

**Step 1: Check the computer.** Detect the OS and CPU architecture (`uname -sm` on macOS; on Windows, `$env:PROCESSOR_ARCHITECTURE` in PowerShell, or `uname -sm` in Git Bash). Note whether `java`, `git`, IntelliJ IDEA, and `brew` (macOS) or `winget` (Windows) are available. IntelliJ is usually at `/Applications/IntelliJ IDEA*.app` on macOS, or under `C:\Program Files\JetBrains\` or `%LOCALAPPDATA%\Programs\` on Windows. Check that there are at least 6 GB of free disk space. Tell me in one line what you found.

**Step 2: Pick the version.** The official NeoForge templates are at https://github.com/NeoForgeMDKs, one repository per Minecraft version, named `MDK-<VERSION>-ModDevGradle` (for example, `MDK-26.3-ModDevGradle`). Use the version I gave above. If it's blank, use the newest Minecraft version that has a `-ModDevGradle` repository. If I named a version with no template, say so and suggest the closest one.

**Step 3: Get the right JDK.** Minecraft 26.x needs **Java 25**. Minecraft 1.20.5 to 1.21.x needs **Java 21**. Confirm it against the template's `build.gradle` (the `JavaLanguageVersion.of(...)` line) after you download it. Use a 64-bit JDK, not just a Java runtime (JRE).

1. If a matching JDK is already installed, use it. On macOS, check with `/usr/libexec/java_home -V`.
2. **On macOS:** the JDK must match the CPU. On Apple Silicon (`arm64`), use an **aarch64** JDK. An x64 JDK under Rosetta causes native library crashes at launch. If Homebrew is available, run `brew install --cask temurin@<VERSION>`; it may ask for my password, so tell me before you run it. Otherwise, install it without admin rights: download `https://api.adoptium.net/v3/binary/latest/<VERSION>/ga/mac/<aarch64|x64>/jdk/hotspot/normal/eclipse` (a `.tar.gz`), extract it into `~/Library/Java/JavaVirtualMachines/`, and confirm that `/usr/libexec/java_home -v <VERSION>` finds it. IntelliJ and Gradle both look for JDKs in that folder.
3. **On Windows:** if `winget` is available, search it (`winget search Temurin`) and install the Eclipse Temurin JDK package for that version. Otherwise, download `https://api.adoptium.net/v3/binary/latest/<VERSION>/ga/windows/<x64|aarch64>/jdk/hotspot/normal/eclipse` (a `.zip`), extract it into `%USERPROFILE%\.jdks\` (where IntelliJ also looks), and set `JAVA_HOME` for my user with `setx`.
4. Check that `java -version` (or `"$JAVA_HOME/bin/java" -version`) reports the right version, and use that `JAVA_HOME` for every Gradle command below.

**Step 4: Install IntelliJ IDEA,** if it isn't installed. JetBrains now ships one IntelliJ IDEA download with free core features. The old separate Community Edition is discontinued, so don't install it. Get it from JetBrains' official sources only:

- **macOS:** with Homebrew, `brew install --cask intellij-idea`. Without Homebrew, download the `.dmg` for my CPU (Apple Silicon or Intel) from https://www.jetbrains.com/idea/download/, then mount it and copy the app to `/Applications` (or `~/Applications` if that needs admin rights).
- **Windows:** with winget, search for `JetBrains IntelliJ IDEA` and install JetBrains' current IntelliJ IDEA package (not the Community one). Without winget, download the installer from https://www.jetbrains.com/idea/download/ and run it, telling me first that an installer window will open.

You can run Steps 4 and 5 at the same time, since both are big downloads.

**Step 5: Download the template.** Put the project in a folder with no spaces or special characters, and **not** in a cloud-synced folder. On macOS, avoid Desktop and Documents, because iCloud often syncs them. A good default is `~/MinecraftMods/examplemod` (on Windows, `%USERPROFILE%\MinecraftMods\examplemod`). Download the template as a zip with `curl` (on Windows, use `curl.exe` or `Invoke-WebRequest`), so `git` isn't required: `https://github.com/NeoForgeMDKs/MDK-<VERSION>-ModDevGradle/archive/refs/heads/main.zip`. If `main` doesn't exist, check the repo's default branch.

Unzip it, move the contents into the project folder, and check that `gradlew`, `gradlew.bat`, and the `gradle/wrapper` folder are there. The template includes the Gradle wrapper, so don't install Gradle separately. On macOS, run `chmod +x gradlew`.

**Step 6: Make it a hello-world mod.** Keep the template's structure, and make these small changes so I can see it's mine:

- Set the mod ID to `examplemod`, the display name to "Example Mod", and the Java package to `com.example.examplemod`. Update every place the template uses them: the `mod_*` properties in `gradle.properties`, `neoforge.mods.toml` if it hardcodes any, the `@Mod` annotation and `MODID` constant, and the package folders.
- In the mod's constructor, log one clear line at startup: `Hello from Example Mod! The mod loaded correctly.`

Keep everything else as the template ships it.

**Step 7: Build.** In the project folder, run `./gradlew build` (on Windows, `gradlew.bat build`) with the right `JAVA_HOME`. The first run downloads Gradle, Minecraft, and NeoForge, so it takes a while. Run it in the background, watch the output, and post a status line every few minutes. When it succeeds, the mod jar is in `build/libs/`. If it fails, read the error and fix it yourself. Common causes: the wrong Java version, a typo from renaming in Step 6, a cloud-synced folder, or a network timeout (just run it again). Doing this first build from the terminal means IntelliJ will open the project quickly later.

**Step 8: Test it from the terminal.** Tell me that a Minecraft window is about to open. Then run `./gradlew runClient` (`gradlew.bat runClient` on Windows) in the background, sending its output to a log file. The window stays open until I close it, so don't wait for the command to finish. Instead, watch the log until your hello line appears and the game reaches the title screen. The client runs offline with a random player name, so there's no sign-in.

- If the log shows a crash, fix it and launch again. On a Mac, if you see native library or LWJGL errors, check that the JDK architecture matches the CPU (Step 3). ModDevGradle normally adds the macOS `-XstartOnFirstThread` flag for you, so only add it to the client run's JVM arguments if the error says the window must be created on the main thread.
- When the hello line appears, ask me to confirm that I can see the title screen, and that **Mods** lists "Example Mod". Then ask me to close the game window.

**Step 9: Open it in IntelliJ.** Open the project folder in IntelliJ from the command line. On macOS, use `open -na "<path to IntelliJ IDEA.app>" --args <project folder>`. On Windows, use `idea64.exe <project folder>` from IntelliJ's `bin` folder. Then tell me exactly what to click, in order:

1. If IntelliJ asks about a license or trial, choose the free option. No account is needed for it.
2. When it asks whether to trust the project, choose **Trust Project**.
3. Wait for the Gradle sync to finish (watch the progress bar at the bottom). Because the terminal build already ran, this should take a few minutes. During the sync, ModDevGradle creates run configurations named after the template's runs, such as **Client**, **Server**, and **Data**.
4. If the sync fails with a Java version error, open **Settings → Build, Execution, Deployment → Build Tools → Gradle** (on macOS, **IntelliJ IDEA → Settings**), set **Gradle JVM** to the Temurin JDK from Step 3, and click the **Reload All Gradle Projects** button (the circular arrows) in the Gradle tool window. Also make sure **File → Project Structure → Project → SDK** is the same JDK.
5. Choose **Client** from the run configuration dropdown at the top of the window, and click the green **Run** button.

When I say I've clicked Run, read `runs/client/logs/latest.log` in the project folder to confirm that your hello line is there, and tell me it worked. If I get stuck, ask me what I see on screen and walk me through it. Don't make me hunt through menus on my own.

**Step 10: Leave notes for later.** Create a short `CLAUDE.md` in the project folder with: the NeoForge and Minecraft versions, the Java version and `JAVA_HOME` path, the commands to build (`./gradlew build`) and test (`./gradlew runClient`, or the **Client** run configuration in IntelliJ), where the jar ends up, where the game log is (`runs/client/logs/latest.log`), and the mod ID and package. Also note that after editing `build.gradle` or `gradle.properties`, IntelliJ needs a Gradle reload. This lets a future Claude Code session pick up the project straight away. If `git` is installed, run `git init` and commit the working setup, so any later change can be undone. If it isn't installed, skip this and don't install it.

**When it works,** give me a short summary:

- the project folder, NeoForge version, Minecraft version, and Java version;
- how to test my mod: click Run on **Client** in IntelliJ, or run `./gradlew runClient`;
- how to work with you on it: keep IntelliJ open to look at the code, and open Claude Code in the project folder to make changes. After you change code, I just click Run again. If you change Gradle files, I click the Gradle reload button first;
- how to use the built jar in the real game: install NeoForge for the same Minecraft version from https://neoforged.net in my normal launcher, then put the jar from `build/libs/` into the `mods` folder;
- two or three example ideas that are a good size for a first mod, such as "a ruby ore and a set of ruby tools", "a block that launches players into the air", or "a /heal command".

Then ask me: **"What mod do you want to build?"** Stop there and wait for my answer. When I describe it, build it in this project, run `./gradlew build` after each change, and tell me when it's ready for me to click Run in IntelliJ.
