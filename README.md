# GSoC 2026 — Jitsi

<img width="888" height="392" alt="image" src="https://github.com/user-attachments/assets/c1d43df6-c12b-4ad0-80df-21333c02bd69" />

#

Contributor: Bandhan Majumder

Email: bandhanmajumder16@gmail.com

GitHub: [bandhan-majumder](https://github.com/bandhan-majumder)

X (Twitter): [@MEbandhan](https://x.com/MEbandhan)

LinkedIn: [Bandhan Majumder](https://linkedin.com/in/bandhan-majumder)

Organization: Jitsi

Mentors: Hristo Terezov and Horatiu Muresan

Project: [Document Picture-in-Picture for browser meetings](https://summerofcode.withgoogle.com/programs/2026/projects/DrgSMJPQ)

Duration: 175 hours

#

### Abstract

This project brings Picture-in-Picture support to Jitsi Meet browser meetings, allowing users to keep their call visible while switching tabs or working in other applications.

Document Picture-in-Picture is used on supported browsers, enabling interactive controls such as microphone mute/unmute, camera toggle, and hangup directly from the floating window. Traditional Video Picture-in-Picture is used as a fallback on browsers where Video PiP is supported.

The feature supports both standalone and embedded Jitsi meetings, with automatic and manual PiP entry flows and a compact layout optimized for multitasking. On Chromium-based browsers, PiP can open automatically when the user switches to another tab, subject to browser permission and user-activation policies. On WebKit-based browsers, Video PiP can be triggered when the meeting window loses focus.

For iframe-based meetings, the embedding page manages the Document PiP window while the meeting iframe streams the selected video through a native `RTCPeerConnection`. A dedicated React PiP bundle receives and renders the track while reusing the existing Always-on-Top toolbar and avatar components. Electron and browsers without Document PiP continue to use traditional Video PiP where supported.

I also tested the iframe implementation across different origins. The Jitsi instance was deployed on an EC2 instance with a subdomain and HTTPS configured through Let’s Encrypt, while NGINX reverse-proxied the domain to the locally running Jitsi instance. A separate embedding test page was deployed on Render.

#

### Project Goals

The project goals were based on the original proposal, with the final scope refined around the compact layout and essential meeting controls.

- Document Picture-in-Picture support: Add Document PiP to Jitsi Meet browser meetings so users can keep an interactive meeting window always on top while working in other applications. This includes support for meetings embedded through the Jitsi IFrame API.
- Interactive PiP controls: Allow users to mute or unmute their microphone, enable or disable their camera, and leave the meeting directly from the Document PiP window.
- Compact PiP layout: Provide a compact layout that keeps the relevant meeting participant visible while using minimal screen space.
- Video PiP fallback: Use traditional Video Picture-in-Picture on browsers without Document PiP where Video PiP is supported.

#

### Implementation Phases

The project followed a phased implementation, starting with browser-level Document Picture-in-Picture support and then extending the same experience to embedded Jitsi meetings.

#### Phase 1: Document PiP support with action buttons and compact layout

The first phase focused on bringing Picture-in-Picture support to standalone Jitsi Meet browser meetings. I implemented Document Picture-in-Picture as the primary experience while retaining traditional Video PiP as a fallback for supported browsers without Document PiP.

The Document PiP window was integrated with the existing meeting state and controls, allowing users to interact with the meeting without returning to the main tab. This included microphone mute/unmute, camera on/off, and leaving the meeting directly from the PiP window. A compact layout was also added to keep the relevant meeting participant visible while using minimal screen space.

Document PiP can be opened manually from the meeting toolbar. On Chromium-based browsers, Document PiP can open automatically when the user switches to another tab, subject to browser permissions. On WebKit-based browsers, Video PiP can be triggered when the meeting window loses focus.

- [PR #17450 – Auto-open Document PiP on tab switch and trigger button](https://github.com/jitsi/jitsi-meet/pull/17450)
- [PR #17576 – Document PiP window content](https://github.com/jitsi/jitsi-meet/pull/17576)

#### Phase 2: Document PiP support for embedded meetings

The second phase extended the browser PiP implementation to meetings running through Jitsi’s IFrame API. Because an embedded meeting runs inside an iframe, opening and managing Document PiP required coordination with the embedding page instead of relying only on the meeting frame.

The meeting iframe requests the PiP window through the existing External API transport. The embedding page owns the Document PiP window and loads a dedicated React renderer bundle. The meeting iframe sends the selected video track through a native `RTCPeerConnection`, while the PiP renderer receives the track and manages the video element, toolbar, participant information, and avatar fallback.

The implementation supports both automatic and configurable manual entry flows. Electron remains on the existing Video PiP implementation, and browsers without Document PiP use Video PiP where supported.

This phase was followed by several compatibility and reliability improvements, including configuration gating for browser PiP, fallback configuration defaults, Electron-specific handling, Safari muted-video behavior, development styling fixes, and a Safari Video PiP black-screen fix.

- [PR #17587 – Iframe Document PiP on tab switch](https://github.com/jitsi/jitsi-meet/pull/17587)
- [PR #17694 – Gate browser PiP behind a configuration flag](https://github.com/jitsi/jitsi-meet/pull/17694)
- [PR #17684 – Fix black video in Safari Video PiP](https://github.com/jitsi/jitsi-meet/pull/17684)
- [PR #17711 – Add a default fallback for the browser PiP configuration](https://github.com/jitsi/jitsi-meet/pull/17711)
- [PR #17712 – Fix the development styling gap](https://github.com/jitsi/jitsi-meet/pull/17712)
- [PR #17713 – Hide PiP trigger buttons in Electron](https://github.com/jitsi/jitsi-meet/pull/17713)
- [PR #17721 – Show the avatar when video is muted in Safari PiP](https://github.com/jitsi/jitsi-meet/pull/17721)

#

### Key Achievements

- Implemented Picture-in-Picture for browser meetings using Document PiP with traditional Video PiP as a fallback where supported.
- Extended Document PiP to meetings embedded through the Jitsi IFrame API.
- Added interactive controls for microphone mute/unmute, camera on/off, and leaving the meeting.
- Implemented a compact PiP layout focused on the relevant meeting participant.
- Added automatic and manual PiP entry flows, subject to browser capabilities and permission policies.
- Added a dedicated React renderer bundle for iframe-owned Document PiP windows.
- Implemented native `RTCPeerConnection` transport between the meeting iframe and the PiP renderer.
- Reused the existing Always-on-Top toolbar, avatar, participant-name, and control components.

### Benefits to Jitsi Users

- Seamless multitasking: Users can keep the meeting visible in an always-on-top PiP window while switching tabs or working in other applications.
- Broader browser support: Document PiP is used where supported, with traditional Video PiP available as a fallback where supported.
- Interactive controls: Users can mute or unmute audio, enable or disable video, and leave the meeting directly from the Document PiP window.
- Embedded meeting support: Document PiP is available to applications using the Jitsi IFrame API.
- Compact viewing experience: The compact layout keeps the relevant participant visible while minimizing the amount of screen space used.
- Improved platform compatibility: Explicit handling preserves the existing Electron behavior and improves PiP behavior on Safari and Chromium-based browsers.

#

### Acknowledgements

I am grateful to have had [Hristo Terezov](https://github.com/hristoterezov) and [Horatiu Muresan](https://github.com/horymury) as my mentors throughout this journey. Their reviews, guidance, and feedback helped me improve both the project and my understanding of the Jitsi codebase.

They were always approachable whenever I had questions or needed clarification, which made the contribution process much easier. I am also thankful to the Jitsi community for the welcoming discussions, feedback, and support throughout the project.

I learned and grew significantly during this experience, both technically and as an open-source contributor.
