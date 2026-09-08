# Family Archive Architecture

M1-C5.1: Define Family Archive Architecture

Status: Architecture proposal for Jason's review. This document defines the reusable model; it does not create archive sites or configure hosting.

## 1. Purpose

Provide a lasting, person-centered home for family history beneath JasonRKelly.com. Each person's archive can begin with a small collection and grow to include additional media, memories, and context without changing its public identity or printed QR codes.

BRK is the first implementation and proof of concept. CWK and DAK inform the shared architecture but are future implementations.

## 2. Design Principles

- A subdomain represents a person, not a collection, event, or media format.
- Keep the person-level address stable as content grows.
- Support optional content types; an archive only displays sections that have content.
- Use a repeatable structure with person-specific content and presentation details.
- Preserve original media and maintain a clear relationship between originals and web derivatives.
- Keep published pages compatible with plain HTML, CSS, and optional JavaScript. No framework, package manager, database, PHP, or build system is required.
- Keep GitHub authoritative for website source, architecture, and publishable content records.
- Follow feature branches, Pull Requests, and Jason's final review authority.

## 3. Subdomain Namespace

| Person key | Person | Planned public address | Implementation status |
| --- | --- | --- | --- |
| `brk` | Bruce R. Kelly | `https://brk.jasonrkelly.com/` | First proof of concept |
| `cwk` | C. W. Kelly | `https://cwk.jasonrkelly.com/` | Future; not built in this scope |
| `dak` | D. A. Kelly | `https://dak.jasonrkelly.com/` | Future; not built in this scope |

Use lowercase person keys consistently in paths and content identifiers. These addresses are planned namespaces, not a statement that DNS, HTTPS, or archive hosting is already configured. Future person keys should be checked for collisions before assignment.

## 4. Person Archive Structure

Each person has a landing page with their name, introductory context, and links to available content. Collections may group related items beneath that person, and may contain multiple content types.

Proposed repository layout for a future implementation:

```text
archives/
  brk/
    index.html
    assets/
      styles.css
      site.js                 # Optional
    items/
      brk-0001/
        index.html            # Stable item page and descriptive context
    collections/
      collection-slug/
        index.html            # Optional grouping of existing items
    media/
      audio/
      video/
      photos/
      documents/
```

Create only directories needed for actual content. CWK and DAK can later use the same structure under their own person keys; do not create placeholder sites now. Recipes and stories can be HTML item pages with optional associated media, so they do not require media directories of their own.

The hosting model should map each person subdomain to its corresponding archive directory. Under that mapping, `archives/brk/items/brk-0001/index.html` is served as `https://brk.jasonrkelly.com/items/brk-0001/`.

Prefer relative links within an archive so it can be previewed locally and served from its own document root. Each archive should be independently servable. Reuse the structure and styling conventions initially; any later shared asset location must be deliberately designed and tested across subdomains.

## 5. Content Type Model

An item is a stable record with one or more content types and optional collection membership. A story with photographs or a recipe with a scanned handwritten card remains one item with multiple associated representations.

| Content type | Static presentation | Associated material |
| --- | --- | --- |
| Audio | HTML audio player and contextual text | Web audio, transcript when available |
| Video | HTML video player and contextual text | Web video, poster image, captions when available |
| Photos | Images with captions and descriptions | Sized images and thumbnails |
| Recipes | Readable HTML ingredients and instructions | Optional scans, photos, or related stories |
| Documents | Descriptive HTML page with view/download links | Web document or page images |
| Stories | HTML narrative | Optional supporting media and references |
| Future types | An appropriate static page or linked file | Additional representations without changing the person namespace |

Use a consistent metadata convention: stable item ID, title, person key, content type or types, description, date or approximate date if known, source or contributor if known, publication permission/status, and associated media references. Record unknown details as unknown rather than inventing them. Collection membership, transcript, captions, and related-item links are optional.

For the proof of concept, metadata can be maintained directly in HTML and a documented media inventory; no runtime data service or generated page pipeline is required. Keep private provenance and storage details out of public pages. Preserve item IDs and published URLs when reorganizing collections.

## 6. Media Preservation Model

Maintain two distinct layers:

- **Archival masters:** Original received files, preserved without overwriting, resizing, recompression, or destructive metadata editing. For physical material, retain the original scan or capture as the digital preservation master and record its source.
- **Web derivatives:** Copies prepared for browsing, playback, thumbnails, or downloads. Derivatives may be regenerated or replaced while their masters remain intact.

Maintain an inventory relating each item and derivative to its master. Record original filename, stable identifier, format, checksum, storage location, and derivative creation details where available. Preserve more than one backup of masters and verify integrity periodically. A hosting copy is not the sole preservation copy.

Do not assume that archival masters belong in the public website or ordinary Git history. Large or private masters may use separately managed archival storage, with a private inventory where necessary. GitHub remains the source of truth for website code, documentation, and publishable content records; external master storage is a documented preservation dependency, not an alternate place to edit website source.

Select the master storage location, backup procedure, derivative formats, and handling of large web media during implementation review. No storage service or media migration is established by this document.

## 7. QR Code Strategy

Printed QR codes should encode the stable person-level HTTPS address, such as `https://brk.jasonrkelly.com/`. They should not point to a particular collection, media file, temporary host, or third-party short link.

The landing page guides visitors to current content. New collections and media can therefore be added without replacing printed codes. Print the readable address alongside the QR code when practical.

Before printing, validate the final address, HTTPS, mobile landing page, and scanning behavior. If hosting changes later, preserve the person subdomain and its landing page address.

## 8. BRK Proof-of-Concept Scope

The next implementation should establish Bruce R. Kelly's landing page, the reusable archive directory pattern, and a small set of Jason-approved content. The first content types depend on available material; implementing every type is not required.

The proof of concept should demonstrate:

- Navigation from the person landing page to at least one item.
- Stable item identity and a structure that allows later collections and content types.
- Appropriate static rendering or playback for the selected media.
- A traceable master-to-derivative relationship for published media.
- Readable mobile presentation, descriptive links, and applicable text alternatives.
- Local validation followed by the normal Pull Request review process.

M1-C5.1 delivers this architecture document only. Building BRK pages, processing media, creating QR artwork, changing DNS or hosting, and deploying are separate implementation work. CWK and DAK sites are outside the BRK proof-of-concept scope.

## 9. Future CWK and DAK Compatibility

CWK and DAK should reuse the person key, landing page, stable item ID, optional collection, and media preservation conventions. Their available content may differ from BRK, and neither should inherit mandatory empty sections or BRK-specific collection names.

Keep person names and biographical content separate from reusable layout conventions. Validate BRK decisions against the possibility of an archive containing only stories, only photographs, or a mixture of recipes, documents, audio, and video. No CWK or DAK directories, pages, media, or hosting configuration are created in this milestone task.

## 10. Repository / Hosting Considerations

Use this repository for the proposed `archives/<person-key>/` website source alongside the existing main site. Keep the current root website intact. The proposed directory tree is a future layout, not a description of files created by this document.

Before implementing hosting, verify how the existing deployment checkout and document root are configured. Confirm that person subdomains can serve their intended archive directories, that HTTPS is available, and that deployment can update the required files through the approved GitHub Actions workflow. Do not assume the current deployment already supports these mappings.

Serve only intended public content. Keep masters, private inventories, credentials, and repository internals outside publicly served locations. Review media size and delivery needs before adding large assets to Git; any alternative media hosting must retain documented ownership and source relationships.

No build step is introduced. Local previews should test each archive as its own document root, including navigation, media URLs, filename case, and missing files. Hosting and workflow adjustments require separately scoped review; none are made here.

## 11. Security and Change-Control Considerations

Jason selects material for publication and confirms that family privacy and permission concerns are addressed. Treat files served by a public static site as public; unlinked URLs and client-side hiding are not access controls. Any requirement for restricted family access needs a separate hosting-level access design before publication.

Follow `AGENTS.md` and the repository development responsibility model:

1. Jason defines requirements and approves implementation direction; jAIson assists with architecture and troubleshooting.
2. Codex or Jason implements scoped changes on `feature/<short-name>`.
3. Jason reviews the diff and performs or approves local validation.
4. Approved work is committed with its milestone reference, pushed to the feature branch, and submitted through a Pull Request.
5. Jason remains the final reviewer and authority to approve merges into `main`.
6. GitHub Actions deploys approved changes entering `main`, followed by production verification.

Codex must not merge Pull Requests, push directly to `main`, deploy directly, edit production files on the host, or modify secrets, credentials, or SSH keys. Branch deletion requires explicit instruction, and destructive Git operations require explicit approval. GitHub remains the authoritative website source and review history.

For this M1-C5.1 task, only `FAMILY_ARCHIVE.md` is created locally. No commit, push, merge, deployment, or configuration change is included.

## 12. M1-C5 Architecture Decisions

The following decisions are proposed for Jason's review:

| Decision | Direction |
| --- | --- |
| Archive identity | One person per subdomain; collections live beneath that identity. |
| Initial namespace | BRK, CWK, and DAK map to the people listed above. |
| Persistent entry point | QR codes use the person-level HTTPS root. |
| Reusable organization | Person landing page, stable item pages, optional collections, and web media. |
| Content model | Optional, extensible types; an item may include multiple types. |
| Preservation | Retain originals as archival masters; web copies are derivatives. |
| Implementation order | BRK first; CWK and DAK influence the model without being built now. |
| Website technology | No-build static HTML, CSS, and optional JavaScript. |
| Source organization | Proposed `archives/<person-key>/` directories in this repository. |
| Hosting direction | Map each subdomain to its person directory, subject to hosting verification. |
| Change authority | Feature branches and Pull Requests; Jason has final merge authority. |

Open implementation choices include the initial BRK content set, master storage and backup arrangements, derivative formats and sizes, any large-media delivery service, and exact DNS/document-root/deployment configuration. Resolve those choices in subsequent scoped work without changing the person-level identity or preservation principles.
