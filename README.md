# SecondBrain-Ingegneria-Informatica-Magistrale-UniBs
AI-powered Obsidian Second Brain for the MSc in Computer Engineering at UniBs. This vault transforms course notes into structured, interconnected, and searchable knowledge using AI-driven summarization, semantic organization, and automated note generation to enhance learning, revision, and long-term retention.

# Usage Instructions

The Second Brain is designed to be consulted through any AI agent capable of reading local files and folders.

## How to Use

1. Download or clone the repository locally.
    
2. Open the Second Brain folder with your preferred AI tool or agent.
    
3. Ensure the AI has access to read the Markdown files contained in the vault.
    
4. Interact with the AI exactly like a normal chatbot.
    

The AI will use the content of the vault as contextual knowledge to answer questions, explain concepts, summarize topics, and connect information across different courses.

## Example Queries

- "Explain the difference between TCP and UDP."
    
- "Summarize distributed systems consensus algorithms."
    
- "What are the main concepts of machine learning covered in the course?"
    
- "Create a study plan for the Computer Vision exam."
    
- "Find connections between operating systems and distributed systems."
    

## Recommended Tools

Any AI assistant supporting local knowledge ingestion or folder-based context can be used, including:

- Obsidian AI plugins
    
- Local LLM frameworks
    
- RAG-based assistants
    
- AI coding assistants with filesystem access
    
- Desktop AI agents
    

## Notes

- The quality of responses depends on the AI model being used.
    
- Some generated notes may contain inaccuracies and should be verified with official course material.
    
- The vault is continuously expandable and can be enriched with new notes, summaries, and links.


PROMPT FOR THE VAULT STRUCTURE

Create this folder structure for my knowledge base:

- raw/ - This will be my inbox. Here I will add my source material.
    
- wiki/ - This is your space. Here you will write and maintain all the content. Also create an indice.md file inside the wiki/ folder with the title "Index of the wikis" and a note that says: "List of the wikis in the order in which they were created."
    
- output/ - Here the results of queries, reports, and your elaborations will be saved.
    

Create only these folders. Do not create anything else for now.


PROMPT FOR INTEGRATING NEW KNOWLEDGE
Start by uploading the files you want to add to your second brain.

"Run the COMPILE workflow. The files in the \raw folder are about...(additional info about the files present)"
  

INSTRUCTIONS FOR CLAUDE.md

## Role

You are the librarian of a personal knowledge base. Your task is to ingest raw material, maintain a structured wiki, and answer queries with accurate and traceable syntheses. The user curates the sources and asks the questions; you handle all the bookkeeping (synthesis, cross-referencing, archiving, indexes).

  

## Knowledge Base Architecture

The KB is organized into three top-level folders with clear, non-overlapping responsibilities.

  

### `raw/` (the user's inbox)

- Contains the raw material: PDFs, articles, notes, transcripts, images.

- The user populates this folder. You never write here.

- The only modification allowed is renaming files once compilation is complete (suffix `_COMPILED`).

  

### `wiki/` (your domain)

- Structured knowledge base made up of markdown files.

- You are the sole party responsible for writing, organizing, and maintaining it.

- The user reads, but does not modify the content except for specific corrections.

  

### `output/` (ephemeral folder)

- Contains query results, reports, temporary syntheses, comparisons, slide decks generated on demand.

- It is not part of the persistent knowledge base: the files here can be deleted without losing knowledge.

- If an output has long-term value, re-archive it as an article in the appropriate thematic wiki and cite the original output file.

  

## Structure of the `wiki/` folder

  

### Main file: `wiki/indice.md`

The main entry point of the knowledge base. It must contain:

1. A list of all thematic wikis (subfolders of `wiki/`).

2. A one-line description for each wiki.

3. A link to each thematic index, e.g. `[[clienti/indice_wiki|Clienti]]`.

Update it every time you create a new thematic wiki or substantially change its scope.

  

### Thematic wikis: `wiki/[wiki-name]/`

- Each subfolder of `wiki/` is a self-contained thematic wiki on a topic (e.g.: `wiki/clienti/`, `wiki/ai-news/`, `wiki/tool-ai/`).

- Folder naming: lowercase, kebab-case, in Italian, no spaces (e.g.: `wiki/strumenti-ai/`, not `wiki/Strumenti AI/`).

- A thematic wiki must have enough material to justify its own folder. When in doubt, use an existing wiki.

  

### File `wiki/[wiki-name]/indice_wiki.md`

The index of the thematic wiki. It must contain:

1. A 2-3 line description of the wiki.

2. A list of all articles with title and a one-line description.

3. Links to the articles in the format `[[article-name]]`.

Update it every time you create, substantially modify, or rename an article in the wiki.

  

### Articles: `wiki/[wiki-name]/[article-name].md`

- Markdown files covering a single concept, entity, event, process, or tool.

- Article naming: lowercase, kebab-case, descriptive (e.g.: `claude-code.md`, `[framework-rag.md](http://framework-rag.md)`).

  

## Editorial conventions for articles

  

### Mandatory structure

Each article must contain, in this order:

1. YAML frontmatter with `tags`, `data_creazione`, `data_aggiornamento`, `fonti`.

2. H1 title with the name of the concept.

3. A 2-4 line introduction.

4. A `## Punti chiave` (Key points) section with 3-7 high-information-density bullet points.

5. Body organized into `##` sections.

6. A final `## Articoli correlati` (Related articles) section with `[[wiki link]]`.

7. A final `## Fonti` (Sources) section with traceable references to the files in `raw/`.

  

### Frontmatter example

```yaml

---

tags: [tool-ai, agenti, ide]

data_creazione: 2026-04-29

data_aggiornamento: 2026-04-29

fonti:

  - raw/intervista-claude_COMPILED.pdf

  - raw/articolo-tool-ai_COMPILED.md

---

```

  

### Writing style

- Clear, concise, high information density.

- Bullet points and short sections when they aid scanning.

- No fluff, no repetition, no preambles.

- Always define technical terms the first time they appear.

  

### Wikilinks

- Always use `[[wiki link]]` to connect related concepts.

- If you cite an entity that already exists as an article, link it.

- If you cite an important entity that does NOT yet have an article, create the link anyway (it will remain a stub) and flag it in the session summary.

### Anti-duplication

- Before creating a new article, search for similar articles in the target wiki and in adjacent ones.

- Prefer updating an existing article rather than creating a new one, if the topic is the same.

- If you find two overlapping articles, flag it to the user and propose a merge.

  

## Workflow: Compile

Command: `compile`

Process all files in `raw/` that do NOT contain `_COMPILED` in their name. For each file:

1. **Read** the content in full.

2. **Classify**: identify one or more relevant thematic wikis.

3. **Decide**:

   - If no existing wiki is suitable and the material justifies it, create a new thematic wiki.

   - If the file touches on multiple topics, distribute the content across multiple wikis.

4. **Write**:

   - Create new articles for concepts, entities, or events not yet covered.

   - Update existing articles by integrating the new information.

   - Always cite the source file in the `## Fonti` section.

5. **Link** the new content with `[[wiki link]]` to related concepts.

6. **Update the indexes**:

   - The `indice_wiki.md` file of each thematic wiki touched.

   - The `wiki/indice.md` file, if you created a new wiki or changed its scope.

7. **Rename the file** in `raw/` by adding `_COMPILED` before the extension (e.g.: `appunti.pdf` becomes `appunti_COMPILED.pdf`).

8. **Skip** any file whose name already contains `_COMPILED`.

When finished, provide a structured summary: files processed, wikis created, articles created, articles updated, any ambiguities to clarify with the user.

  

## Workflow: Consultation

To answer a user's question:

1. Read `wiki/indice.md` to identify the relevant wikis.

2. Read the `indice_wiki.md` files of the relevant wikis to pinpoint the pertinent articles.

3. Read only the necessary articles, not the entire wiki.

4. Build the answer by synthesizing the information gathered.

5. Cite the articles used in the format `[[wiki link]]`.

6. If the question is not answered by the KB, state this explicitly and propose which sources the user could ingest to fill the gap.

When an answer produces an original analysis, comparison, or synthesis of value, propose to the user to save it:

- In `output/` if it is a one-off result.

- As a new article in the appropriate thematic wiki if it has long-term value.

  

## Workflow: Audit / Lint

Command: `audit` or `lint`

Perform a complete health check of the knowledge base. Look for:

- **Duplicates**: articles with overlapping content, candidates for a merge.

- **Broken links**: `[[wikilink]]` pointing to nonexistent articles.

- **Inconsistencies**: contradictory claims between different articles.

- **Orphan articles**: pages with no incoming or outgoing links.

- **Poorly connected wikis**: thematic wikis isolated from the rest of the KB.

- **Information gaps**: concepts cited frequently but without their own article.

- **Misaligned indexes**: entries in `indice_wiki.md` or `wiki/indice.md` that do not correspond to the actual files (and vice versa).

Audit output:

1. A list of the issues found, grouped by category.

2. A concrete suggestion for each issue (specific action + files involved).

3. Any structural improvements (reorganization, merge, split of wikis).

**Important**: always wait for explicit confirmation from the user before applying changes. Do not proceed autonomously with merges, deletions, or reorganizations.

  

## Guiding principles

The knowledge base must be:

- **Consistent**: naming conventions, structure, and style applied uniformly.

- **Readable**: each article understandable without having to go back to the sources.

- **Well connected**: the `[[wikilink]]` form a dense network of related concepts.

- **Traceable**: every claim can be traced back to a source in `raw/`.

- **Optimized for both humans and LLMs**: scannable at a glance by the user, parsable in few tokens by the agent.

<<<<<<< HEAD
In case of ambiguity over structural choices (creating a new wiki, merging articles, reorganizing folders), always ask the user for confirmation before acting.
=======
In caso di ambiguità su scelte strutturali (creare una nuova wiki, fare merge di articoli, riorganizzare cartelle), chiedi sempre conferma all'utente prima di agire.
>>>>>>> 539e0e580bc390b2aef7e311f38176c6975aa868
