 (cd "$(git rev-parse --show-toplevel)" && git apply --3way <<'EOF' 
diff --git a/.bazelrc b/.bazelrc
new file mode 100644
index 0000000..074345d
--- /dev/null
+++ b/.bazelrc
@@ -0,0 +1 @@
+build --symlink_prefix=build/
diff --git a/.gitignore b/.gitignore
index 37657d4..4f18bd4 100644
--- a/.gitignore
+++ b/.gitignore
@@ -1,2 +1,6 @@
-Sidekick/Logic/View Controllers/Tools/Slide Studio/Resources/bin/marp
+apps/poseidon_swift/Sidekick/Logic/View Controllers/Tools/Slide Studio/Resources/bin/marp
 .DS_Store
+
+# Ignore model files
+libs/whisper_cpp/models/*
+libs/llama_cpp/models/*
diff --git a/BUILD.bazel b/BUILD.bazel
new file mode 100644
index 0000000..e69de29
diff --git a/README.md b/README.md
index a9a5dfb..8653bdc 100644
--- a/README.md
+++ b/README.md
@@ -1,242 +1,19 @@
-<h1 align="center">
-  <img src="https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/appIcon.png" width = "200" height = "200">
-  <br />
-  Sidekick
-</h1>
+# TheCollective Monorepo
 
-<p align="center">
-<img alt="Downloads" src="https://img.shields.io/github/downloads/johnbean393/Sidekick/total?label=Downloads" height=22.5>
-<img alt="License" src="https://img.shields.io/github/license/johnbean393/Sidekick?label=License" height=22.5>
-</p>
+This repository assembles multiple AI applications and libraries in a single Bazel workspace. The layout follows the plan in `AGENTS.md`.
 
-Chat with a local LLM that can respond with information from your files, folders and websites on your Mac without installing any other software. All conversations happen offline, and your data stays secure. Sidekick is a <strong>local first</strong> application –– with a built in inference engine for local models, while accommodating OpenAI compatible APIs for additional model options.
+```
+TheCollective/
+  apps/      # End user applications
+  libs/      # Shared libraries
+  tools/     # Developer tools
+  archive/   # Legacy projects
+```
 
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/demoScreenshot.png)
+Build everything with:
 
-## Example Use
+```bash
+bazel build //apps/...
+```
 
-Let’s say you're collecting evidence for a History paper about interactions between Aztecs and Spanish troops, and you’re looking for text about whether the Aztecs used captured Spanish weapons.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Experts/demoHistoryScreenshot.png)
-
-Here, you can ask Sidekick, “Did the Aztecs use captured Spanish weapons?”, and it responds with direct quotes with page numbers and a brief analysis.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Experts/demoHistorySource.png)
-
-To verify Sidekick’s answer, just click on the references displayed below Sidekick’s answer, and the academic paper referenced by Sidekick immediately opens in your viewer.
-
-## Features
-
-Read more about Sidekick's features and how to use them [here](https://johnbean393.github.io/Sidekick/).
-
-### Resource Use
-
-Sidekick accesses files, folders, and websites from your experts, which can be individually configured to contain resources related to specific areas of interest. Activating an expert allows Sidekick to fetch and reference materials as needed.
-
-Because Sidekick uses RAG (Retrieval Augmented Generation), you can theoretically put unlimited resources into each expert, and Sidekick will still find information relevant to your request to aid its analysis.
-
-For example, a student might create the experts `English Literature`, `Mathematics`, `Geography`, `Computer Science` and `Physics`. In the image below, he has activated the expert `Computer Science`.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Experts/demoExpertUse.png)
-
-Users can also give Sidekick access to files just by dragging them into the input field.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/demoTemporaryResource.png)
-
-Sidekick can even respond with the latest information using **web search**, speeding up research.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Web%20Search/webSearch.png)
-
-### Bring Your Own API Key
-
-In addition to its core local-first capabilities, Sidekick now offers an option to bring your own key for OpenAI compatible APIs. This allows you to tap into additional remote models while still preserving a primarily local-first workflow.
-
-### Reasoning Model Support
-
-Sidekick supports a variety of reasoning models, including DeepSeek's DeepSeek-R1 and *hybrid* reasoning models such as Alibaba Cloud's Qwen3.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/reasoningModelSupport.png)
-
-When using hybrid reasoning models such as Qwen3, reasoning can be toggled on/off. As you type your prompt, Sidekick automatically analyses it and determines if reasoning is needed. To explicitly toggle reasoning, click the `Reason` button in the prompt bar.
-
-### Function Calling
-
-Sidekick can call functions to boost the mathematical and logical capabilities of models, and to execute actions. Functions are called sequentially in a loop until a result is obtained.
-
-For example, when asking Sidekick to reverse a string or do arithmetic operation, it runs tools, then presents the result.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Function%20Calling/functionCalling.png)
-
-When telling Sidekick to draft an invitation email for a birthday celebration to my friend Jean, Sidekick finds my birthday and Jean's email address from my contacts book, and creates a draft in my default email client. 
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Function%20Calling/functionCallingDraftEmail.png)
-
-This enables agents running fully locally. 
-
-### Memory
-
-Sidekick can now remember helpful information between conversations, making its responses more relevant and personalized. Whether you're typing, speaking, or generating images in Sidekick, it can recall details and preferences you’ve shared and use them to tailor its responses. The more you use it, the more useful it becomes, and you’ll start to notice improvements over time.
-
-For example, I might tell Sidekick that I am a beginner in Python trying to create my own version of Tetris.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Memory/memoryRemember.png)
-
-When I ask it about `pygame` alternatives, it makes recommendations based on my current project, Tetris.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Memory/memoryUse.png)
-
-### Canvas
-
-Create, edit and preview websites, code and other textual content using Canvas.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Canvas/canvasWebsite.png)
-
-Select parts of the text, then prompt the chatbot to perform selective edits.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Canvas/canvasSelectiveEdit.png)
-
-### Image Generation
-
-Sidekick can generate images from text, allowing you to create visual aids for your work. 
-
-There are no buttons, no switches to flick, no `Image Generation` mode. Instead, a built-in CoreML model **automatically identifies** image generation prompts, and generates an image when necessary.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Image%20Generation/imageGeneration.png)
-
-Image generation is available on macOS 15.2 or above, and requires Apple Intelligence.
-
-### Advanced Markdown Rendering
-
-Markdown is rendered beautifully in Sidekick.
-
-#### LaTeX
-
-Sidekick offers native LaTeX rendering for mathematical equations.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/latexRendering1.png)
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/latexRendering2.png)
-
-#### Data Visualization
-
-Visualizations are automatically generated for tables when appropriate, with a variety of charts available, including bar charts, line charts and pie charts.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/dataVisualization1.png)
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/dataVisualization2.png)
-
-Charts can be dragged and dropped into third party apps.
-
-#### Code
-
-Code is beautifully rendered with syntax highlighting, and can be exported or copied at the click of a button.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/codeExport.png)
-
-### Toolbox
-
-Use **Tools** in Sidekick to supercharge your workflow.
-
-#### Inline Writing Assistant
-
-Press `Command + Control + I` to access Sidekick's inline writing assistant. For example, use the `Answer Question` command to do your homework without leaving Microsoft Word!
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Inline%20Writing%20Assistant/inlineWritingAssistantCommands.png)
-
-Use the default keyboard shortcut `Tab` to accept suggestions for the next word, or `Shift + Tab` to accept all suggested words. View a demo [here](https://drive.google.com/file/d/1DDzdNHid7MwIDz4tgTpnqSA-fuBCajQA/preview).
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Inline%20Writing%20Assistant/inlineWritingAssistantCompletions.png)
-
-#### Detector
-
-Use Detector to evaluate the AI percentage of text, and use provided suggestions to rewrite AI content.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Detector/detectorEvaluationResults.png)
-
-#### Diagrammer
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Diagrammer/diagrammerPrompt.png)
-
-Diagrammer allows you to swiftly generate intricate relational diagrams all from a prompt. Take advantage of the integrated preview and editor for quick edits.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Diagrammer/diagrammerPreviewEditor2.png)
-
-#### Slide Studio
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Slide%20Studio/slideStudioPrompt.png)
-
-Instead of making a PowerPoint, just write a prompt. Use AI to craft 10-minute presentations in just 5 minutes.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Slide%20Studio/slideStudioPreviewEditor.png)
-
-Export to common formats like PDF and PowerPoint.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Slide%20Studio/slideStudioExport.png)
-
-### Fast Generation
-
-Sidekick uses `llama.cpp` as its inference backend, which is optimized to deliver lightning fast generation speeds on Apple Silicon. It also supports speculative decoding, which can further improve the generation speed.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Local%20Models/speculativeDecodingSupport.png)
-
-Optionally, you can offload generation to speed up processing while extending the battery life of your MacBook.
-
-![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Remote%20Models/remoteModelSettingsTop.png)
-
-## Installation
-
-**Requirements**
-- A Mac with Apple Silicon
-- RAM ≥ 8 GB
-
-**Download and Setup**
-- Follow the guide [here](https://johnbean393.github.io/Sidekick/Markdown/gettingStarted/).
-
-## Goals
-
-The main goal of Sidekick is to make open, local, private, and contextually aware AI applications accessible to the masses.
-
-Read more about our mission [here](https://johnbean393.github.io/Sidekick/Markdown/About/mission/).
-
-## Developer Setup
-
-**Requirements**
-- A Mac with Apple Silicon
-- RAM ≥ 8 GB
-
-### Developer Setup Instructions
-1. Clone this repository.
-1. Run `security find-identity -p codesigning -v` to find your signing identity.
-   - You'll see something like
-   - `  1) <SIGNING IDENTITY> "Apple Development: Michael DiGovanni ( XXXXXXXXXX)"`
-1. Run `./setup.sh <TEAM_NAME> <SIGNING IDENTITY FROM STEP 2>` to change the team in the Xcode project and download and sign the `marp` binary.
-   - The `marp` binary is required for building and must be signed to create presentations.
-1. Open and run in Xcode.
-
-## Contributing
-
-Contributions are very welcome. Let's make Sidekick simple and powerful.
-
-## Contact
-
-Contact this repository's owner at johnbean393@gmail.com, or file an issue.
-
-## Credits
-
-This project would not be possible without the hard work of:
-
-- psugihara and contributors who built [FreeChat](https://github.com/psugihara/FreeChat), which this project took heavy inspiration from
-- Georgi Gerganov for [llama.cpp](https://github.com/ggerganov/llama.cpp)
-- Alibaba for training Qwen 2.5
-- Meta for training Llama 3
-- Google for training Gemma 3
-
-## Star History
-
-<a href="https://star-history.com/#johnbean393/Sidekick&Date">
- <picture>
-   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=johnbean393/Sidekick&type=Date&theme=dark" />
-   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=johnbean393/Sidekick&type=Date" />
-   <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=johnbean393/Sidekick&type=Date" />
- </picture>
-</a>
+Refer to `apps/poseidon_swift/README.md` for the Swift client and other READMEs for additional components.
diff --git a/WORKSPACE b/WORKSPACE
new file mode 100644
index 0000000..147a819
--- /dev/null
+++ b/WORKSPACE
@@ -0,0 +1 @@
+workspace(name = "the_collective")
diff --git a/apps/localai/BUILD.bazel b/apps/localai/BUILD.bazel
new file mode 100644
index 0000000..c6ab4d1
--- /dev/null
+++ b/apps/localai/BUILD.bazel
@@ -0,0 +1 @@
+# TODO: add build rules
diff --git a/apps/localai/README.md b/apps/localai/README.md
new file mode 100644
index 0000000..3dcd8de
--- /dev/null
+++ b/apps/localai/README.md
@@ -0,0 +1 @@
+# localai
diff --git a/apps/ollama_cli/BUILD.bazel b/apps/ollama_cli/BUILD.bazel
new file mode 100644
index 0000000..c6ab4d1
--- /dev/null
+++ b/apps/ollama_cli/BUILD.bazel
@@ -0,0 +1 @@
+# TODO: add build rules
diff --git a/apps/ollama_cli/README.md b/apps/ollama_cli/README.md
new file mode 100644
index 0000000..50b4a1d
--- /dev/null
+++ b/apps/ollama_cli/README.md
@@ -0,0 +1 @@
+# ollama_cli
diff --git a/apps/openhands/BUILD.bazel b/apps/openhands/BUILD.bazel
new file mode 100644
index 0000000..c6ab4d1
--- /dev/null
+++ b/apps/openhands/BUILD.bazel
@@ -0,0 +1 @@
+# TODO: add build rules
diff --git a/apps/openhands/README.md b/apps/openhands/README.md
new file mode 100644
index 0000000..4e1f607
--- /dev/null
+++ b/apps/openhands/README.md
@@ -0,0 +1 @@
+# openhands
diff --git a/apps/poseidon/BUILD.bazel b/apps/poseidon/BUILD.bazel
new file mode 100644
index 0000000..c6ab4d1
--- /dev/null
+++ b/apps/poseidon/BUILD.bazel
@@ -0,0 +1 @@
+# TODO: add build rules
diff --git a/apps/poseidon/README.md b/apps/poseidon/README.md
new file mode 100644
index 0000000..e1c30cc
--- /dev/null
+++ b/apps/poseidon/README.md
@@ -0,0 +1 @@
+# poseidon
diff --git a/apps/poseidon_dashboard/BUILD.bazel b/apps/poseidon_dashboard/BUILD.bazel
new file mode 100644
index 0000000..c6ab4d1
--- /dev/null
+++ b/apps/poseidon_dashboard/BUILD.bazel
@@ -0,0 +1 @@
+# TODO: add build rules
diff --git a/apps/poseidon_dashboard/README.md b/apps/poseidon_dashboard/README.md
new file mode 100644
index 0000000..e96fb1a
--- /dev/null
+++ b/apps/poseidon_dashboard/README.md
@@ -0,0 +1 @@
+# poseidon_dashboard
diff --git a/apps/poseidon_scientist/BUILD.bazel b/apps/poseidon_scientist/BUILD.bazel
new file mode 100644
index 0000000..c6ab4d1
--- /dev/null
+++ b/apps/poseidon_scientist/BUILD.bazel
@@ -0,0 +1 @@
+# TODO: add build rules
diff --git a/apps/poseidon_scientist/README.md b/apps/poseidon_scientist/README.md
new file mode 100644
index 0000000..e534659
--- /dev/null
+++ b/apps/poseidon_scientist/README.md
@@ -0,0 +1 @@
+# poseidon_scientist
diff --git a/About/mission.md b/apps/poseidon_swift/About/mission.md
similarity index 100%
rename from About/mission.md
rename to apps/poseidon_swift/About/mission.md
diff --git a/apps/poseidon_swift/BUILD.bazel b/apps/poseidon_swift/BUILD.bazel
new file mode 100644
index 0000000..36aa7aa
--- /dev/null
+++ b/apps/poseidon_swift/BUILD.bazel
@@ -0,0 +1,16 @@
+load("@build_bazel_rules_apple//apple:ios.bzl", "ios_application")
+load("@rules_swift//swift:swift.bzl", "swift_library")
+
+swift_library(
+    name = "sidekick_lib",
+    srcs = glob(["Sidekick/**/*.swift"]),
+)
+
+ios_application(
+    name = "sidekick",
+    bundle_id = "com.example.sidekick",
+    families = ["iphone", "ipad"],
+    infoplists = ["Sidekick/Info.plist"],
+    minimum_os_version = "13.0",
+    deps = [":sidekick_lib"],
+)
diff --git a/Docs Images/Features/Canvas/canvasDataVisualization.png b/apps/poseidon_swift/Docs Images/Features/Canvas/canvasDataVisualization.png
similarity index 100%
rename from Docs Images/Features/Canvas/canvasDataVisualization.png
rename to apps/poseidon_swift/Docs Images/Features/Canvas/canvasDataVisualization.png
diff --git a/Docs Images/Features/Canvas/canvasExport.png b/apps/poseidon_swift/Docs Images/Features/Canvas/canvasExport.png
similarity index 100%
rename from Docs Images/Features/Canvas/canvasExport.png
rename to apps/poseidon_swift/Docs Images/Features/Canvas/canvasExport.png
diff --git a/Docs Images/Features/Canvas/canvasSelectiveEdit.png b/apps/poseidon_swift/Docs Images/Features/Canvas/canvasSelectiveEdit.png
similarity index 100%
rename from Docs Images/Features/Canvas/canvasSelectiveEdit.png
rename to apps/poseidon_swift/Docs Images/Features/Canvas/canvasSelectiveEdit.png
diff --git a/Docs Images/Features/Canvas/canvasWebsite.png b/apps/poseidon_swift/Docs Images/Features/Canvas/canvasWebsite.png
similarity index 100%
rename from Docs Images/Features/Canvas/canvasWebsite.png
rename to apps/poseidon_swift/Docs Images/Features/Canvas/canvasWebsite.png
diff --git a/Docs Images/Features/Conversations/chatSettings.png b/apps/poseidon_swift/Docs Images/Features/Conversations/chatSettings.png
similarity index 100%
rename from Docs Images/Features/Conversations/chatSettings.png
rename to apps/poseidon_swift/Docs Images/Features/Conversations/chatSettings.png
diff --git a/Docs Images/Features/Conversations/codeExport.png b/apps/poseidon_swift/Docs Images/Features/Conversations/codeExport.png
similarity index 100%
rename from Docs Images/Features/Conversations/codeExport.png
rename to apps/poseidon_swift/Docs Images/Features/Conversations/codeExport.png
diff --git a/Docs Images/Features/Conversations/copyRaw.png b/apps/poseidon_swift/Docs Images/Features/Conversations/copyRaw.png
similarity index 100%
rename from Docs Images/Features/Conversations/copyRaw.png
rename to apps/poseidon_swift/Docs Images/Features/Conversations/copyRaw.png
diff --git a/Docs Images/Features/Conversations/dataVisualization1.png b/apps/poseidon_swift/Docs Images/Features/Conversations/dataVisualization1.png
similarity index 100%
rename from Docs Images/Features/Conversations/dataVisualization1.png
rename to apps/poseidon_swift/Docs Images/Features/Conversations/dataVisualization1.png
diff --git a/Docs Images/Features/Conversations/dataVisualization2.png b/apps/poseidon_swift/Docs Images/Features/Conversations/dataVisualization2.png
similarity index 100%
rename from Docs Images/Features/Conversations/dataVisualization2.png
rename to apps/poseidon_swift/Docs Images/Features/Conversations/dataVisualization2.png
diff --git a/Docs Images/Features/Conversations/dataVisualizationDrag.png b/apps/poseidon_swift/Docs Images/Features/Conversations/dataVisualizationDrag.png
similarity index 100%
rename from Docs Images/Features/Conversations/dataVisualizationDrag.png
rename to apps/poseidon_swift/Docs Images/Features/Conversations/dataVisualizationDrag.png
diff --git a/Docs Images/Features/Conversations/demoTemporaryResource.png b/apps/poseidon_swift/Docs Images/Features/Conversations/demoTemporaryResource.png
similarity index 100%
rename from Docs Images/Features/Conversations/demoTemporaryResource.png
rename to apps/poseidon_swift/Docs Images/Features/Conversations/demoTemporaryResource.png
diff --git a/Docs Images/Features/Conversations/latexRendering1.png b/apps/poseidon_swift/Docs Images/Features/Conversations/latexRendering1.png
similarity index 100%
rename from Docs Images/Features/Conversations/latexRendering1.png
rename to apps/poseidon_swift/Docs Images/Features/Conversations/latexRendering1.png
diff --git a/Docs Images/Features/Conversations/latexRendering2.png b/apps/poseidon_swift/Docs Images/Features/Conversations/latexRendering2.png
similarity index 100%
rename from Docs Images/Features/Conversations/latexRendering2.png
rename to apps/poseidon_swift/Docs Images/Features/Conversations/latexRendering2.png
diff --git a/Docs Images/Features/Conversations/reasoningModelSupport.png b/apps/poseidon_swift/Docs Images/Features/Conversations/reasoningModelSupport.png
similarity index 100%
rename from Docs Images/Features/Conversations/reasoningModelSupport.png
rename to apps/poseidon_swift/Docs Images/Features/Conversations/reasoningModelSupport.png
diff --git a/Docs Images/Features/Experts/demoExpertUse.png b/apps/poseidon_swift/Docs Images/Features/Experts/demoExpertUse.png
similarity index 100%
rename from Docs Images/Features/Experts/demoExpertUse.png
rename to apps/poseidon_swift/Docs Images/Features/Experts/demoExpertUse.png
diff --git a/Docs Images/Features/Experts/demoHistoryScreenshot.png b/apps/poseidon_swift/Docs Images/Features/Experts/demoHistoryScreenshot.png
similarity index 100%
rename from Docs Images/Features/Experts/demoHistoryScreenshot.png
rename to apps/poseidon_swift/Docs Images/Features/Experts/demoHistoryScreenshot.png
diff --git a/Docs Images/Features/Experts/demoHistorySource.png b/apps/poseidon_swift/Docs Images/Features/Experts/demoHistorySource.png
similarity index 100%
rename from Docs Images/Features/Experts/demoHistorySource.png
rename to apps/poseidon_swift/Docs Images/Features/Experts/demoHistorySource.png
diff --git a/Docs Images/Features/Experts/editExpert.png b/apps/poseidon_swift/Docs Images/Features/Experts/editExpert.png
similarity index 100%
rename from Docs Images/Features/Experts/editExpert.png
rename to apps/poseidon_swift/Docs Images/Features/Experts/editExpert.png
diff --git a/Docs Images/Features/Experts/expertList.png b/apps/poseidon_swift/Docs Images/Features/Experts/expertList.png
similarity index 100%
rename from Docs Images/Features/Experts/expertList.png
rename to apps/poseidon_swift/Docs Images/Features/Experts/expertList.png
diff --git a/Docs Images/Features/Experts/expertPromptExamples.png b/apps/poseidon_swift/Docs Images/Features/Experts/expertPromptExamples.png
similarity index 100%
rename from Docs Images/Features/Experts/expertPromptExamples.png
rename to apps/poseidon_swift/Docs Images/Features/Experts/expertPromptExamples.png
diff --git a/Docs Images/Features/Experts/manageExperts.png b/apps/poseidon_swift/Docs Images/Features/Experts/manageExperts.png
similarity index 100%
rename from Docs Images/Features/Experts/manageExperts.png
rename to apps/poseidon_swift/Docs Images/Features/Experts/manageExperts.png
diff --git a/Docs Images/Features/Experts/resourceUseSettings.png b/apps/poseidon_swift/Docs Images/Features/Experts/resourceUseSettings.png
similarity index 100%
rename from Docs Images/Features/Experts/resourceUseSettings.png
rename to apps/poseidon_swift/Docs Images/Features/Experts/resourceUseSettings.png
diff --git a/Docs Images/Features/Experts/selectExpertMenu.png b/apps/poseidon_swift/Docs Images/Features/Experts/selectExpertMenu.png
similarity index 100%
rename from Docs Images/Features/Experts/selectExpertMenu.png
rename to apps/poseidon_swift/Docs Images/Features/Experts/selectExpertMenu.png
diff --git a/Docs Images/Features/Function Calling/functionCalling.png b/apps/poseidon_swift/Docs Images/Features/Function Calling/functionCalling.png
similarity index 100%
rename from Docs Images/Features/Function Calling/functionCalling.png
rename to apps/poseidon_swift/Docs Images/Features/Function Calling/functionCalling.png
diff --git a/Docs Images/Features/Function Calling/functionCallingDraftEmail.png b/apps/poseidon_swift/Docs Images/Features/Function Calling/functionCallingDraftEmail.png
similarity index 100%
rename from Docs Images/Features/Function Calling/functionCallingDraftEmail.png
rename to apps/poseidon_swift/Docs Images/Features/Function Calling/functionCallingDraftEmail.png
diff --git a/Docs Images/Features/Function Calling/functionsToggle.png b/apps/poseidon_swift/Docs Images/Features/Function Calling/functionsToggle.png
similarity index 100%
rename from Docs Images/Features/Function Calling/functionsToggle.png
rename to apps/poseidon_swift/Docs Images/Features/Function Calling/functionsToggle.png
diff --git a/Docs Images/Features/Image Generation/imageGeneration.png b/apps/poseidon_swift/Docs Images/Features/Image Generation/imageGeneration.png
similarity index 100%
rename from Docs Images/Features/Image Generation/imageGeneration.png
rename to apps/poseidon_swift/Docs Images/Features/Image Generation/imageGeneration.png
diff --git a/Docs Images/Features/Local Models/modelLibrary.png b/apps/poseidon_swift/Docs Images/Features/Local Models/modelLibrary.png
similarity index 100%
rename from Docs Images/Features/Local Models/modelLibrary.png
rename to apps/poseidon_swift/Docs Images/Features/Local Models/modelLibrary.png
diff --git a/Docs Images/Features/Local Models/modelSelector.png b/apps/poseidon_swift/Docs Images/Features/Local Models/modelSelector.png
similarity index 100%
rename from Docs Images/Features/Local Models/modelSelector.png
rename to apps/poseidon_swift/Docs Images/Features/Local Models/modelSelector.png
diff --git a/Docs Images/Features/Local Models/modelToolbarMenu.png b/apps/poseidon_swift/Docs Images/Features/Local Models/modelToolbarMenu.png
similarity index 100%
rename from Docs Images/Features/Local Models/modelToolbarMenu.png
rename to apps/poseidon_swift/Docs Images/Features/Local Models/modelToolbarMenu.png
diff --git a/Docs Images/Features/Local Models/speculativeDecodingSupport.png b/apps/poseidon_swift/Docs Images/Features/Local Models/speculativeDecodingSupport.png
similarity index 100%
rename from Docs Images/Features/Local Models/speculativeDecodingSupport.png
rename to apps/poseidon_swift/Docs Images/Features/Local Models/speculativeDecodingSupport.png
diff --git a/Docs Images/Features/Memory/memories.png b/apps/poseidon_swift/Docs Images/Features/Memory/memories.png
similarity index 100%
rename from Docs Images/Features/Memory/memories.png
rename to apps/poseidon_swift/Docs Images/Features/Memory/memories.png
diff --git a/Docs Images/Features/Memory/memoryRemember.png b/apps/poseidon_swift/Docs Images/Features/Memory/memoryRemember.png
similarity index 100%
rename from Docs Images/Features/Memory/memoryRemember.png
rename to apps/poseidon_swift/Docs Images/Features/Memory/memoryRemember.png
diff --git a/Docs Images/Features/Memory/memorySettings.png b/apps/poseidon_swift/Docs Images/Features/Memory/memorySettings.png
similarity index 100%
rename from Docs Images/Features/Memory/memorySettings.png
rename to apps/poseidon_swift/Docs Images/Features/Memory/memorySettings.png
diff --git a/Docs Images/Features/Memory/memoryUse.png b/apps/poseidon_swift/Docs Images/Features/Memory/memoryUse.png
similarity index 100%
rename from Docs Images/Features/Memory/memoryUse.png
rename to apps/poseidon_swift/Docs Images/Features/Memory/memoryUse.png
diff --git a/Docs Images/Features/Remote Models/remoteModelSettingsBottom.png b/apps/poseidon_swift/Docs Images/Features/Remote Models/remoteModelSettingsBottom.png
similarity index 100%
rename from Docs Images/Features/Remote Models/remoteModelSettingsBottom.png
rename to apps/poseidon_swift/Docs Images/Features/Remote Models/remoteModelSettingsBottom.png
diff --git a/Docs Images/Features/Remote Models/remoteModelSettingsTop.png b/apps/poseidon_swift/Docs Images/Features/Remote Models/remoteModelSettingsTop.png
similarity index 100%
rename from Docs Images/Features/Remote Models/remoteModelSettingsTop.png
rename to apps/poseidon_swift/Docs Images/Features/Remote Models/remoteModelSettingsTop.png
diff --git a/Docs Images/Features/Remote Models/remoteModelSetup.png b/apps/poseidon_swift/Docs Images/Features/Remote Models/remoteModelSetup.png
similarity index 100%
rename from Docs Images/Features/Remote Models/remoteModelSetup.png
rename to apps/poseidon_swift/Docs Images/Features/Remote Models/remoteModelSetup.png
diff --git a/Docs Images/Features/Tools/Detector/detectorEvaluationResults.png b/apps/poseidon_swift/Docs Images/Features/Tools/Detector/detectorEvaluationResults.png
similarity index 100%
rename from Docs Images/Features/Tools/Detector/detectorEvaluationResults.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Detector/detectorEvaluationResults.png
diff --git a/Docs Images/Features/Tools/Diagrammer/diagrammerPreviewEditor1.png b/apps/poseidon_swift/Docs Images/Features/Tools/Diagrammer/diagrammerPreviewEditor1.png
similarity index 100%
rename from Docs Images/Features/Tools/Diagrammer/diagrammerPreviewEditor1.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Diagrammer/diagrammerPreviewEditor1.png
diff --git a/Docs Images/Features/Tools/Diagrammer/diagrammerPreviewEditor2.png b/apps/poseidon_swift/Docs Images/Features/Tools/Diagrammer/diagrammerPreviewEditor2.png
similarity index 100%
rename from Docs Images/Features/Tools/Diagrammer/diagrammerPreviewEditor2.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Diagrammer/diagrammerPreviewEditor2.png
diff --git a/Docs Images/Features/Tools/Diagrammer/diagrammerPrompt.png b/apps/poseidon_swift/Docs Images/Features/Tools/Diagrammer/diagrammerPrompt.png
similarity index 100%
rename from Docs Images/Features/Tools/Diagrammer/diagrammerPrompt.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Diagrammer/diagrammerPrompt.png
diff --git a/Docs Images/Features/Tools/Diagrammer/exportedDiagram.png b/apps/poseidon_swift/Docs Images/Features/Tools/Diagrammer/exportedDiagram.png
similarity index 100%
rename from Docs Images/Features/Tools/Diagrammer/exportedDiagram.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Diagrammer/exportedDiagram.png
diff --git a/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCommandPalette.png b/apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCommandPalette.png
similarity index 100%
rename from Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCommandPalette.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCommandPalette.png
diff --git a/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCommands.png b/apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCommands.png
similarity index 100%
rename from Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCommands.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCommands.png
diff --git a/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCompletions.png b/apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCompletions.png
similarity index 100%
rename from Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCompletions.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCompletions.png
diff --git a/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCompletionsExcludedApps.png b/apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCompletionsExcludedApps.png
similarity index 100%
rename from Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCompletionsExcludedApps.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantCompletionsExcludedApps.png
diff --git a/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantEditingCommand.png b/apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantEditingCommand.png
similarity index 100%
rename from Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantEditingCommand.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantEditingCommand.png
diff --git a/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantSettings.png b/apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantSettings.png
similarity index 100%
rename from Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantSettings.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Inline Writing Assistant/inlineWritingAssistantSettings.png
diff --git a/Docs Images/Features/Tools/Slide Studio/slideStudioExport.png b/apps/poseidon_swift/Docs Images/Features/Tools/Slide Studio/slideStudioExport.png
similarity index 100%
rename from Docs Images/Features/Tools/Slide Studio/slideStudioExport.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Slide Studio/slideStudioExport.png
diff --git a/Docs Images/Features/Tools/Slide Studio/slideStudioPreviewEditor.png b/apps/poseidon_swift/Docs Images/Features/Tools/Slide Studio/slideStudioPreviewEditor.png
similarity index 100%
rename from Docs Images/Features/Tools/Slide Studio/slideStudioPreviewEditor.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Slide Studio/slideStudioPreviewEditor.png
diff --git a/Docs Images/Features/Tools/Slide Studio/slideStudioPrompt.png b/apps/poseidon_swift/Docs Images/Features/Tools/Slide Studio/slideStudioPrompt.png
similarity index 100%
rename from Docs Images/Features/Tools/Slide Studio/slideStudioPrompt.png
rename to apps/poseidon_swift/Docs Images/Features/Tools/Slide Studio/slideStudioPrompt.png
diff --git a/Docs Images/Features/Web Search/tavilyApi.png b/apps/poseidon_swift/Docs Images/Features/Web Search/tavilyApi.png
similarity index 100%
rename from Docs Images/Features/Web Search/tavilyApi.png
rename to apps/poseidon_swift/Docs Images/Features/Web Search/tavilyApi.png
diff --git a/Docs Images/Features/Web Search/webSearch.png b/apps/poseidon_swift/Docs Images/Features/Web Search/webSearch.png
similarity index 100%
rename from Docs Images/Features/Web Search/webSearch.png
rename to apps/poseidon_swift/Docs Images/Features/Web Search/webSearch.png
diff --git a/Docs Images/Features/Web Search/webSearchFunction.png b/apps/poseidon_swift/Docs Images/Features/Web Search/webSearchFunction.png
similarity index 100%
rename from Docs Images/Features/Web Search/webSearchFunction.png
rename to apps/poseidon_swift/Docs Images/Features/Web Search/webSearchFunction.png
diff --git a/Docs Images/Features/Web Search/webSearchSettings.png b/apps/poseidon_swift/Docs Images/Features/Web Search/webSearchSettings.png
similarity index 100%
rename from Docs Images/Features/Web Search/webSearchSettings.png
rename to apps/poseidon_swift/Docs Images/Features/Web Search/webSearchSettings.png
diff --git a/Docs Images/Getting Started/firstChat.png b/apps/poseidon_swift/Docs Images/Getting Started/firstChat.png
similarity index 100%
rename from Docs Images/Getting Started/firstChat.png
rename to apps/poseidon_swift/Docs Images/Getting Started/firstChat.png
diff --git a/Docs Images/Getting Started/mainInterface.png b/apps/poseidon_swift/Docs Images/Getting Started/mainInterface.png
similarity index 100%
rename from Docs Images/Getting Started/mainInterface.png
rename to apps/poseidon_swift/Docs Images/Getting Started/mainInterface.png
diff --git a/Docs Images/Getting Started/mainInterfaceAnnotated.png b/apps/poseidon_swift/Docs Images/Getting Started/mainInterfaceAnnotated.png
similarity index 100%
rename from Docs Images/Getting Started/mainInterfaceAnnotated.png
rename to apps/poseidon_swift/Docs Images/Getting Started/mainInterfaceAnnotated.png
diff --git a/Docs Images/Getting Started/mountedDmg.png b/apps/poseidon_swift/Docs Images/Getting Started/mountedDmg.png
similarity index 100%
rename from Docs Images/Getting Started/mountedDmg.png
rename to apps/poseidon_swift/Docs Images/Getting Started/mountedDmg.png
diff --git a/Docs Images/Getting Started/newWindow.png b/apps/poseidon_swift/Docs Images/Getting Started/newWindow.png
similarity index 100%
rename from Docs Images/Getting Started/newWindow.png
rename to apps/poseidon_swift/Docs Images/Getting Started/newWindow.png
diff --git a/Docs Images/Getting Started/setupSheet.png b/apps/poseidon_swift/Docs Images/Getting Started/setupSheet.png
similarity index 100%
rename from Docs Images/Getting Started/setupSheet.png
rename to apps/poseidon_swift/Docs Images/Getting Started/setupSheet.png
diff --git a/Docs Images/appIcon.png b/apps/poseidon_swift/Docs Images/appIcon.png
similarity index 100%
rename from Docs Images/appIcon.png
rename to apps/poseidon_swift/Docs Images/appIcon.png
diff --git a/Docs Images/demoScreenshot.png b/apps/poseidon_swift/Docs Images/demoScreenshot.png
similarity index 100%
rename from Docs Images/demoScreenshot.png
rename to apps/poseidon_swift/Docs Images/demoScreenshot.png
diff --git a/Features/Tools/detector.md b/apps/poseidon_swift/Features/Tools/detector.md
similarity index 100%
rename from Features/Tools/detector.md
rename to apps/poseidon_swift/Features/Tools/detector.md
diff --git a/Features/Tools/diagrammer.md b/apps/poseidon_swift/Features/Tools/diagrammer.md
similarity index 100%
rename from Features/Tools/diagrammer.md
rename to apps/poseidon_swift/Features/Tools/diagrammer.md
diff --git a/Features/Tools/inlineWritingAssistant.md b/apps/poseidon_swift/Features/Tools/inlineWritingAssistant.md
similarity index 100%
rename from Features/Tools/inlineWritingAssistant.md
rename to apps/poseidon_swift/Features/Tools/inlineWritingAssistant.md
diff --git a/Features/Tools/slideStudio.md b/apps/poseidon_swift/Features/Tools/slideStudio.md
similarity index 100%
rename from Features/Tools/slideStudio.md
rename to apps/poseidon_swift/Features/Tools/slideStudio.md
diff --git a/Features/canvas.md b/apps/poseidon_swift/Features/canvas.md
similarity index 100%
rename from Features/canvas.md
rename to apps/poseidon_swift/Features/canvas.md
diff --git a/Features/conversations.md b/apps/poseidon_swift/Features/conversations.md
similarity index 100%
rename from Features/conversations.md
rename to apps/poseidon_swift/Features/conversations.md
diff --git a/Features/experts.md b/apps/poseidon_swift/Features/experts.md
similarity index 100%
rename from Features/experts.md
rename to apps/poseidon_swift/Features/experts.md
diff --git a/Features/imageGeneration.md b/apps/poseidon_swift/Features/imageGeneration.md
similarity index 100%
rename from Features/imageGeneration.md
rename to apps/poseidon_swift/Features/imageGeneration.md
diff --git a/Features/localModels.md b/apps/poseidon_swift/Features/localModels.md
similarity index 100%
rename from Features/localModels.md
rename to apps/poseidon_swift/Features/localModels.md
diff --git a/Features/remoteModels.md b/apps/poseidon_swift/Features/remoteModels.md
similarity index 100%
rename from Features/remoteModels.md
rename to apps/poseidon_swift/Features/remoteModels.md
diff --git a/Features/webSearch.md b/apps/poseidon_swift/Features/webSearch.md
similarity index 100%
rename from Features/webSearch.md
rename to apps/poseidon_swift/Features/webSearch.md
diff --git a/Markdown/About/mission.md b/apps/poseidon_swift/Markdown/About/mission.md
similarity index 100%
rename from Markdown/About/mission.md
rename to apps/poseidon_swift/Markdown/About/mission.md
diff --git a/Markdown/Features/Tools/detector.md b/apps/poseidon_swift/Markdown/Features/Tools/detector.md
similarity index 100%
rename from Markdown/Features/Tools/detector.md
rename to apps/poseidon_swift/Markdown/Features/Tools/detector.md
diff --git a/Markdown/Features/Tools/diagrammer.md b/apps/poseidon_swift/Markdown/Features/Tools/diagrammer.md
similarity index 100%
rename from Markdown/Features/Tools/diagrammer.md
rename to apps/poseidon_swift/Markdown/Features/Tools/diagrammer.md
diff --git a/Markdown/Features/Tools/inlineWritingAssistant.md b/apps/poseidon_swift/Markdown/Features/Tools/inlineWritingAssistant.md
similarity index 100%
rename from Markdown/Features/Tools/inlineWritingAssistant.md
rename to apps/poseidon_swift/Markdown/Features/Tools/inlineWritingAssistant.md
diff --git a/Markdown/Features/Tools/slideStudio.md b/apps/poseidon_swift/Markdown/Features/Tools/slideStudio.md
similarity index 100%
rename from Markdown/Features/Tools/slideStudio.md
rename to apps/poseidon_swift/Markdown/Features/Tools/slideStudio.md
diff --git a/Markdown/Features/canvas.md b/apps/poseidon_swift/Markdown/Features/canvas.md
similarity index 100%
rename from Markdown/Features/canvas.md
rename to apps/poseidon_swift/Markdown/Features/canvas.md
diff --git a/Markdown/Features/conversations.md b/apps/poseidon_swift/Markdown/Features/conversations.md
similarity index 100%
rename from Markdown/Features/conversations.md
rename to apps/poseidon_swift/Markdown/Features/conversations.md
diff --git a/Markdown/Features/experts.md b/apps/poseidon_swift/Markdown/Features/experts.md
similarity index 100%
rename from Markdown/Features/experts.md
rename to apps/poseidon_swift/Markdown/Features/experts.md
diff --git a/Markdown/Features/functionCalling.md b/apps/poseidon_swift/Markdown/Features/functionCalling.md
similarity index 100%
rename from Markdown/Features/functionCalling.md
rename to apps/poseidon_swift/Markdown/Features/functionCalling.md
diff --git a/Markdown/Features/imageGeneration.md b/apps/poseidon_swift/Markdown/Features/imageGeneration.md
similarity index 100%
rename from Markdown/Features/imageGeneration.md
rename to apps/poseidon_swift/Markdown/Features/imageGeneration.md
diff --git a/Markdown/Features/localModels.md b/apps/poseidon_swift/Markdown/Features/localModels.md
similarity index 100%
rename from Markdown/Features/localModels.md
rename to apps/poseidon_swift/Markdown/Features/localModels.md
diff --git a/Markdown/Features/memory.md b/apps/poseidon_swift/Markdown/Features/memory.md
similarity index 100%
rename from Markdown/Features/memory.md
rename to apps/poseidon_swift/Markdown/Features/memory.md
diff --git a/Markdown/Features/remoteModels.md b/apps/poseidon_swift/Markdown/Features/remoteModels.md
similarity index 100%
rename from Markdown/Features/remoteModels.md
rename to apps/poseidon_swift/Markdown/Features/remoteModels.md
diff --git a/Markdown/Features/webSearch.md b/apps/poseidon_swift/Markdown/Features/webSearch.md
similarity index 100%
rename from Markdown/Features/webSearch.md
rename to apps/poseidon_swift/Markdown/Features/webSearch.md
diff --git a/Markdown/gettingStarted.md b/apps/poseidon_swift/Markdown/gettingStarted.md
similarity index 100%
rename from Markdown/gettingStarted.md
rename to apps/poseidon_swift/Markdown/gettingStarted.md
diff --git a/apps/poseidon_swift/README.md b/apps/poseidon_swift/README.md
new file mode 100644
index 0000000..c6c7197
--- /dev/null
+++ b/apps/poseidon_swift/README.md
@@ -0,0 +1,11 @@
+# Poseidon Swift
+
+This directory contains the Poseidon iOS/macOS client.
+
+To open the project in Xcode:
+
+```bash
+open Sidekick.xcodeproj
+```
+
+Run `./setup.sh <TEAM_NAME> <SIGNING_ID>` to configure signing before building.
diff --git a/apps/poseidon_swift/SIDEKICK_README.md b/apps/poseidon_swift/SIDEKICK_README.md
new file mode 100644
index 0000000..a9a5dfb
--- /dev/null
+++ b/apps/poseidon_swift/SIDEKICK_README.md
@@ -0,0 +1,242 @@
+<h1 align="center">
+  <img src="https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/appIcon.png" width = "200" height = "200">
+  <br />
+  Sidekick
+</h1>
+
+<p align="center">
+<img alt="Downloads" src="https://img.shields.io/github/downloads/johnbean393/Sidekick/total?label=Downloads" height=22.5>
+<img alt="License" src="https://img.shields.io/github/license/johnbean393/Sidekick?label=License" height=22.5>
+</p>
+
+Chat with a local LLM that can respond with information from your files, folders and websites on your Mac without installing any other software. All conversations happen offline, and your data stays secure. Sidekick is a <strong>local first</strong> application –– with a built in inference engine for local models, while accommodating OpenAI compatible APIs for additional model options.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/demoScreenshot.png)
+
+## Example Use
+
+Let’s say you're collecting evidence for a History paper about interactions between Aztecs and Spanish troops, and you’re looking for text about whether the Aztecs used captured Spanish weapons.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Experts/demoHistoryScreenshot.png)
+
+Here, you can ask Sidekick, “Did the Aztecs use captured Spanish weapons?”, and it responds with direct quotes with page numbers and a brief analysis.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Experts/demoHistorySource.png)
+
+To verify Sidekick’s answer, just click on the references displayed below Sidekick’s answer, and the academic paper referenced by Sidekick immediately opens in your viewer.
+
+## Features
+
+Read more about Sidekick's features and how to use them [here](https://johnbean393.github.io/Sidekick/).
+
+### Resource Use
+
+Sidekick accesses files, folders, and websites from your experts, which can be individually configured to contain resources related to specific areas of interest. Activating an expert allows Sidekick to fetch and reference materials as needed.
+
+Because Sidekick uses RAG (Retrieval Augmented Generation), you can theoretically put unlimited resources into each expert, and Sidekick will still find information relevant to your request to aid its analysis.
+
+For example, a student might create the experts `English Literature`, `Mathematics`, `Geography`, `Computer Science` and `Physics`. In the image below, he has activated the expert `Computer Science`.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Experts/demoExpertUse.png)
+
+Users can also give Sidekick access to files just by dragging them into the input field.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/demoTemporaryResource.png)
+
+Sidekick can even respond with the latest information using **web search**, speeding up research.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Web%20Search/webSearch.png)
+
+### Bring Your Own API Key
+
+In addition to its core local-first capabilities, Sidekick now offers an option to bring your own key for OpenAI compatible APIs. This allows you to tap into additional remote models while still preserving a primarily local-first workflow.
+
+### Reasoning Model Support
+
+Sidekick supports a variety of reasoning models, including DeepSeek's DeepSeek-R1 and *hybrid* reasoning models such as Alibaba Cloud's Qwen3.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/reasoningModelSupport.png)
+
+When using hybrid reasoning models such as Qwen3, reasoning can be toggled on/off. As you type your prompt, Sidekick automatically analyses it and determines if reasoning is needed. To explicitly toggle reasoning, click the `Reason` button in the prompt bar.
+
+### Function Calling
+
+Sidekick can call functions to boost the mathematical and logical capabilities of models, and to execute actions. Functions are called sequentially in a loop until a result is obtained.
+
+For example, when asking Sidekick to reverse a string or do arithmetic operation, it runs tools, then presents the result.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Function%20Calling/functionCalling.png)
+
+When telling Sidekick to draft an invitation email for a birthday celebration to my friend Jean, Sidekick finds my birthday and Jean's email address from my contacts book, and creates a draft in my default email client. 
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Function%20Calling/functionCallingDraftEmail.png)
+
+This enables agents running fully locally. 
+
+### Memory
+
+Sidekick can now remember helpful information between conversations, making its responses more relevant and personalized. Whether you're typing, speaking, or generating images in Sidekick, it can recall details and preferences you’ve shared and use them to tailor its responses. The more you use it, the more useful it becomes, and you’ll start to notice improvements over time.
+
+For example, I might tell Sidekick that I am a beginner in Python trying to create my own version of Tetris.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Memory/memoryRemember.png)
+
+When I ask it about `pygame` alternatives, it makes recommendations based on my current project, Tetris.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Memory/memoryUse.png)
+
+### Canvas
+
+Create, edit and preview websites, code and other textual content using Canvas.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Canvas/canvasWebsite.png)
+
+Select parts of the text, then prompt the chatbot to perform selective edits.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Canvas/canvasSelectiveEdit.png)
+
+### Image Generation
+
+Sidekick can generate images from text, allowing you to create visual aids for your work. 
+
+There are no buttons, no switches to flick, no `Image Generation` mode. Instead, a built-in CoreML model **automatically identifies** image generation prompts, and generates an image when necessary.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Image%20Generation/imageGeneration.png)
+
+Image generation is available on macOS 15.2 or above, and requires Apple Intelligence.
+
+### Advanced Markdown Rendering
+
+Markdown is rendered beautifully in Sidekick.
+
+#### LaTeX
+
+Sidekick offers native LaTeX rendering for mathematical equations.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/latexRendering1.png)
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/latexRendering2.png)
+
+#### Data Visualization
+
+Visualizations are automatically generated for tables when appropriate, with a variety of charts available, including bar charts, line charts and pie charts.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/dataVisualization1.png)
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/dataVisualization2.png)
+
+Charts can be dragged and dropped into third party apps.
+
+#### Code
+
+Code is beautifully rendered with syntax highlighting, and can be exported or copied at the click of a button.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Conversations/codeExport.png)
+
+### Toolbox
+
+Use **Tools** in Sidekick to supercharge your workflow.
+
+#### Inline Writing Assistant
+
+Press `Command + Control + I` to access Sidekick's inline writing assistant. For example, use the `Answer Question` command to do your homework without leaving Microsoft Word!
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Inline%20Writing%20Assistant/inlineWritingAssistantCommands.png)
+
+Use the default keyboard shortcut `Tab` to accept suggestions for the next word, or `Shift + Tab` to accept all suggested words. View a demo [here](https://drive.google.com/file/d/1DDzdNHid7MwIDz4tgTpnqSA-fuBCajQA/preview).
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Inline%20Writing%20Assistant/inlineWritingAssistantCompletions.png)
+
+#### Detector
+
+Use Detector to evaluate the AI percentage of text, and use provided suggestions to rewrite AI content.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Detector/detectorEvaluationResults.png)
+
+#### Diagrammer
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Diagrammer/diagrammerPrompt.png)
+
+Diagrammer allows you to swiftly generate intricate relational diagrams all from a prompt. Take advantage of the integrated preview and editor for quick edits.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Diagrammer/diagrammerPreviewEditor2.png)
+
+#### Slide Studio
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Slide%20Studio/slideStudioPrompt.png)
+
+Instead of making a PowerPoint, just write a prompt. Use AI to craft 10-minute presentations in just 5 minutes.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Slide%20Studio/slideStudioPreviewEditor.png)
+
+Export to common formats like PDF and PowerPoint.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Tools/Slide%20Studio/slideStudioExport.png)
+
+### Fast Generation
+
+Sidekick uses `llama.cpp` as its inference backend, which is optimized to deliver lightning fast generation speeds on Apple Silicon. It also supports speculative decoding, which can further improve the generation speed.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Local%20Models/speculativeDecodingSupport.png)
+
+Optionally, you can offload generation to speed up processing while extending the battery life of your MacBook.
+
+![Screenshot](https://raw.githubusercontent.com/johnbean393/Sidekick/refs/heads/main/Docs%20Images/Features/Remote%20Models/remoteModelSettingsTop.png)
+
+## Installation
+
+**Requirements**
+- A Mac with Apple Silicon
+- RAM ≥ 8 GB
+
+**Download and Setup**
+- Follow the guide [here](https://johnbean393.github.io/Sidekick/Markdown/gettingStarted/).
+
+## Goals
+
+The main goal of Sidekick is to make open, local, private, and contextually aware AI applications accessible to the masses.
+
+Read more about our mission [here](https://johnbean393.github.io/Sidekick/Markdown/About/mission/).
+
+## Developer Setup
+
+**Requirements**
+- A Mac with Apple Silicon
+- RAM ≥ 8 GB
+
+### Developer Setup Instructions
+1. Clone this repository.
+1. Run `security find-identity -p codesigning -v` to find your signing identity.
+   - You'll see something like
+   - `  1) <SIGNING IDENTITY> "Apple Development: Michael DiGovanni ( XXXXXXXXXX)"`
+1. Run `./setup.sh <TEAM_NAME> <SIGNING IDENTITY FROM STEP 2>` to change the team in the Xcode project and download and sign the `marp` binary.
+   - The `marp` binary is required for building and must be signed to create presentations.
+1. Open and run in Xcode.
+
+## Contributing
+
+Contributions are very welcome. Let's make Sidekick simple and powerful.
+
+## Contact
+
+Contact this repository's owner at johnbean393@gmail.com, or file an issue.
+
+## Credits
+
+This project would not be possible without the hard work of:
+
+- psugihara and contributors who built [FreeChat](https://github.com/psugihara/FreeChat), which this project took heavy inspiration from
+- Georgi Gerganov for [llama.cpp](https://github.com/ggerganov/llama.cpp)
+- Alibaba for training Qwen 2.5
+- Meta for training Llama 3
+- Google for training Gemma 3
+
