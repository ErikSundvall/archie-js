# Background
This is a directory for background starting prompts and files needed to run the three step process based on https://github.com/snarktank/ai-dev-tasks

## Important: Working Branch

**ALL DEVELOPMENT WORK MUST BE DONE IN THE `javascript-experiments` BRANCH.**

This branch is based on `adl_2.4_support` and contains all necessary setup files and documentation. Do not work in other branches unless explicitly instructed otherwise.

## Deepwiki for documentation
* https://deepwiki.com/openEHR/archie contains comprehensive documentation for the Archie library
* https://deepwiki.com/openEHR/specifications-ITS-BMM exkpains the BMM file format and documents openEHR using BMM.
* Do note that you can ask questions (also via MCP) to Deepwiki and thus offload documentation browsing to deepwiki's optimized AI to save spac in your own token window 
* https://docs.devin.ai/work-with-devin/deepwiki-mcp decribes the Deepwiki MCP API and especially note the section #available-tools. You may want to use the tool `ask_question` to delegate some of your own questions.
* If you can not connect to the MCP server when needed, pause and let's figure out how together first, instead of skipping using deepwiki as help via MCP.

# Other info
the README.md file in this project's root explains a lot about Arcie and its usage. Remember to read that.

# Goals
The current Archie implementation is Java based, we want to be able to run Archie (or equivalent new code) in web browsers without making external server calls other services than to a static website for inital loading, thus e.g. converting Archie to Javascript/Typescript or using WASM are options. 
The converted code will be used in internal developer tools for infomaticians, so inital load times are not critical as long as the code can then be cached in browser.
1. The first use case that we want to cover with our converted code is to, in a HTML/JS-based application, be able to programatically populate a valid openEHR RM-based structure based on an openEHR template and then serialize that object in openEHRs canonical JSON fomrat.
2. Later use cases involve conversion of archetypes/tempaltes between different formats.
3. Finally it would be nice to hava all Archie's features available in Javascritp/Typescript 
We do not want to force users of use case #1 to load everything needed for usecases 2 & 3, so a modular approach is needed.

# Implementation details
* Figure out what code, tests and example setups would be good to convert to Javascript in oeder to reach the initial goals stated above. If systematicall converting everything in Archie at once is better than initially just parts of it, then feel free to suggest that.
* We suggest using Deno, possibly with Vite if that helps (instead of Node.js and NPM) if possible, in order to reduce security vulnerabilties
* Do not modify any existing directories of the Archie repo, since we want to be able to provide the results of this project back as PRs to archie. So create new subdirectories when needed.

# Process steps
## Feature pack 0001, setup
- Make sure this branch (javascript-experiments) is based on https://github.com/ErikSundvall/archie-js/tree/adl_2.4_support, if not rebase or do whatever is needed to fix that.
- Check that all existing tests work
- Check that you (the AI) can access all needed documentation, including using the deepwiki MCP `ask_question` tool

## Feature pack 0002, plan 
Detail and document at least three separate alternative strategies for converting archie to something javascript/typescript-based that can run efficiently in web browswers.
Feel free to suggest anything, but things we have already thought of are:
* Transpiling existing Java code to WASM
* Converting Java code to Kotlin first and then use Kotlin's ability to compile to Javascript/Typescript
* Clean new Javascript/Typescript implementation of Archie's capabilities without transpiling etc.

Stop after this and let developer pick plan

## Feature pack 0003, detail and implement plan chosen by developer
* TBD - further pormpt will be created by developer after reading strategy alternatives



