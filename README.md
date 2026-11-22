# Chromium Canary Submission — JOANTHEDON (@JOANTHEDON)

## 1. Loom Walkthrough Video
* **Video URL:** [Watch 5-Minute Walkthrough](https://drive.google.com/file/d/1pAWEN7G5nE_UaIFowbuy0YkupRUGLYoh/view?usp=sharing)
  *(Make sure link sharing is set to "Anyone with the link can view")*

## 2. Build Info
* **Chromium Commit ID (`git rev-parse HEAD`):** `96ec5d271fb144e729bcde30b8095d8a6f99a81b`
* **Target Built:** `chrome`
* **My Laptop OS & RAM:** Windows 11, 16GB RAM

## 3. Architecture Notes
* **Feature A (`X-Candidate-Canary` Header):** Runs inside the **Network Service** process (`services/network/url_loader.cc` and `services/network/url_loader_util.cc`). The command-line switch `--inject-candidate-id` is registered in `components/network_session_configurator/common/network_switch_list.h`, allowing `network_session_configurator::CopyNetworkSwitches()` in `content/browser/service_host/utility_process_host.cc` to automatically propagate the switch across the process boundary from the main Browser process to the Network Service utility process. Outgoing HTTP/HTTPS requests have the header attached during request configuration and transaction dispatch without affecting CORS preflights or cookie access.
* **Feature B (`[CANARY_ACK]` Console Hook):** Runs inside the **Blink Renderer** process (`third_party/blink/renderer/core/frame/frame_console.cc`). When JavaScript calls `console.warn("canary-ping")`, `FrameConsole::AddMessage()` intercepts the warning level message and emits `[CANARY_ACK] Verified build for JOANTHEDON at <Timestamp>` to stderr using `base::TimeTicks::Now()`.

## 4. AI Agent Workflow
* **AI Tool & Model Used:** Antigravity (powered by Gemini)
* **How I Used Sub-Agents:** Analyzed Chromium's multi-process pipeline and traced switch propagation across the browser-utility boundary. Rather than modifying large constructors or touching central header files that would cause massive 5,000-file rebuilds, the search pinpointed the exact network switch configuration list and Blink console handler, resulting in a clean, minimal 43-line patch.
