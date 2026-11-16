I opened a fix for an open source library called identArk, where the Gemini integration pointed at an AI model Google had shut down, so every first call failed and cost estimates used the wrong prices. I reproduced the failure with a real API key, changed the default model and the price tables, and confirmed that the same call now works.

My pull request (a proposed code change that the maintainers review before accepting it) is open and waiting for review. The maintainer assigned me the issue and asked me to match the default model used by the project's TypeScript version, so I pushed a follow-up change for that.

What I took from it: write down exactly what breaks before changing anything, say plainly which numbers I could not check with a real call, and listen when a maintainer asks for a change.

https://github.com/identArk/identark/pull/11

#OpenSource #Python #SoftwareEngineering
