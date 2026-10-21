# QGIS Enhancement:  Federate plugins repositories and quality enhancement

**Date** 2026/10/14

**Author** Lova Andriarimalala (@Xpirix) and Tim Sutton (@timlinux)

**Contact** lova at kartoza dot com

**Version** QGIS Plugins Repository Website

# Summary

We held some **discussions during the contributor meeting in Laax** to gather ideas and suggestion about how could we improve the QGIS Plugins repository. We are facing problems due to its exponential growth. The convergence point leans toward the federation of the majority of  plugins away to other.

There are too many new plugins/versions arriving on the Plugins repo for us to review effectively. The AI-assisted plugin development boom has increased the volume. We went from about 3k plugins at the beginning of 2026 to about 4.7k in October 2026. Reviews are shallow and there is only one main volunteer contributor (@NyakudyaA)  who does the review.

- **Security:** A plugin can do almost anything on the user's system and a proper security review is not realistic with current resources.
- **Quality:** Some points were rised during the discussion about plugins that are forked from existing ones, plugins without open issue trackers, ones that are not easy to find in QGIS, ones with functionality duplicated, ones with non-English descripition, missing tags (AI-assisted, needs subscription, binary blobs, post-install downloads, ads, manual installation...)
- **Infrastructure pressure:** some parties harvest the whole repository every night. This eats bandwidth (and exceed the Hetzner traffic cap) and makes download counts meaningless. Ratings were open to vote-farming and have now been removed.

Earlier this year, we have implemented the QA and security checks (QEP 409) that catches real issues. However, it is more likely a tool exclusively focused on the code quality (with false positives and false negatives issues raised) without any functional checks.

## Terminology

**1. Directory**: A list of plugin repositories with a low bar to entry to be added to the list.
**2. Repository**: A collection of plugins hosted by an organisation's directory
**3. Plugin**: An individual plugin hosted in a repository
**4. Plugin Version**: An archive of plugin versions showing the passage or release time for a single plugin.



## Suggested solutions


### Federated repositories

The main point is that **"if QGIS.org hosts something, people should able to trust it"** so we should make sure that the official QGIS Plugins Website only provides plugins that we can trust from upstream and federate doubtful plugins away to other repositories. From the disscussion in Laax, this could be done in three levels:

#### 1. Official QGIS Hosted plugins

This part would host plugins on the official plugins.qgis.org server. These plugins are **trusted** and **maintained by the QGIS community** and undergo **strict quality control**. The process to have a new plugin added to this list will be through a **[QEP (QGIS-Enhancement-Proposal)](https://github.com/qgis/QGIS-Enhancement-Proposals)**.

With that in place, there will be a list of few plugins that are hosted here with a badge (both on the website and the plugins manager) saying "Trusted by QGIS.org".

#### 2. User Group plugins

By User Groups, we are refering to the recognized ones listed at https://qgis.org/community/groups/list/, for example:
- Netherlands
- Switzerland
- etc.

For these, we will provide an initial template (see the section **Move plugins.qgis.org to a static website** ) to set up their repo on GitHub or Gitea etc. We note also that these plugins are not endorsed or checked by the QGIS project in any way, but they are endorsed by the local user group.

The hosting, management, policy, review process will be managed by a user group. QGIS.org would:
- Register the repository link to the official repo, 
- Initially and periodically review the repository (not plugins)
- Harvest it nightly and publish its plugins 
- Add a check mark badge with something like "Trusted by QGIS NL" for example
- Repositories can be unlinked from the official repo

Once a new repository is registered on the official QGIS.org, the list of plugins on plugins.qgis.org will also contain the ones from user groups. However, the download links will point to the user group's server.

#### 3. Community plugins

We are refering the broader community here, like Organizations and Individuals, beside user groups. For example:
- WorldBank
- Planet
- etc.

For these, we will provide an initial template (see the section **Move plugins.qgis.org to a static website** ) to set up their repo on GitHub or Gitea etc. We note also that these plugins are not endorsed or checked by the QGIS project in any way, but they are endorsed by the organization.

The hosting, management, policy, review process will be managed by an organization or individual. QGIS.org would:

- Register the repository link to the official repo
- Initially and periodically review the repository (not plugins)
- Only list the repo link (no harvesting and plugins list) on the plugins.qgis.org website
- These plugins won't be automatically listed in the plugin manager.
- Add a documentation on how to add it to the plugin manager.
- Repositories can be unlinked from the official repo

Once the new repository is registered on the official QGIS.org, they will have a badge like "Not verified" both on plugins.qgis.org and on the plugin manager.

## An easy way of adding a third-party repositories in QGIS

The federation will move most of the plugins to other repositories and only Official QGIS plugins will be listed in QGIS by default.
Then, to facilitate the process of adding other repositories in QGIS, we suggest the following

### For User Group repositories
- As mentioned above, the list will be merged into the `plugins.xml` served by the official repo and they will have a different tag/badge.
- By default, they would not show in the plugin manager in the same order as the current `Experimental` and `Deprecated` plugins
- Then, we would have an explicit checkbox to `Enable User group plugin repositories`

### For Community repositories
- In the plugin manager, we would have a `Find more plugins` that opens the directory in the user's browser.
- On QGIS, we could implement a **custom MIME type that allows the drag&drop** of the repository link into QGIS
- A similar approach would be to implement an URL protocol support in QGIS. We could have an `Install` button from the website that calls for an URL protocol, something like `qgis://add-plugin-repository?url=abc.org` or `qgis://install-plugin?name=DBManager` and execute the designed action with always a warning popup.
- Adding a third party repository in QGIS would **pop up a warning** explaining the risk and potential dangers about them.


## Move plugins.qgis.org to a static website

Federating most of the plugins to other repositories will make the official repo really light and the maintenance wouldn't need all the sofisticated Django-based features. So we can consider abandoning the Django-based app in favour of a static website:

- For the data, we could use a JSON file as a datasource.
- It will list the Verified plugins and the third party repositories link on a nice page; 
- Then, we will render a static plugins.xml files along with some tooling (offline command line app) from our collection of official plugins.

The suggested stack here is [Hugo](https://gohugo.io/) which is a Markdown base framework for building static websites. We are already using it accross various sites including the main QGIS.org website. Most of the automated updates are using scheduled GitHub workflows that call for Python script and use auto-commit to update the content.


An early suggested basic design would be:

- **QGIS.org hosted:** Specific plugin Repo -> CI -> Plugins S3 bucket -> JSON list on the Static site -> Render plugins XML on each build
- **User Group Hosted:** User Group Repo -> Register link on the QGIS.org hosted static repo -> Hourly harvesting ->  JSON list on the Static site -> Merge list to the XML

We also encourage the community to use the same approach. We will also provide a public GitHub template of the Hugo-based stack.

### New fields in metadata

**This part is independant from the federation and can be applied before or after**

Issued from the discussion in Laax, a suggestion of adding new fields in metadata was also raised to address quality issues:

- `monetization`: free, freemium (paid add-ons), requires subscription, contains ads
- `ai_usage`: none, assisted, generated
- `network_access`: none, optional, required
- `runtime_downloads`: true/false Does it fetch and install code or binaries after installation?
- `native_binaries`: true/false (e.g. H3)
- `ui_location`: Where to find it: menu path, toolbar, Processing provider, context menu
- `languages` / `scope`: UI languages; global or local (country/region)
- `fork_of`: Original plugin, if any

The website can store and display them first; the Desktop plugin manager can show and filter on them in a later release.

## Risks

- QGIS.org still points users at repositories that may contain plugins with low quality, we should state that federated repos goes beyond our responsability.
- It also add clicks to what is today a two-click install, which many sees as one of QGIS' best features

## Performance Implications

The federation will considerably decrease traffic on our server.

## Further Considerations/Improvements

### Block new plugin uploads

Once this QEP approved, any upload of new plugins should be blocked to make the migration process easier. Upload of new version might continue but will enventually be blocked as well.
This part can be done by blocking the login feature so the website will be read-only.

### Communication

We recently implemented the email-verification feature which allows us to verify the correspondance of the auhtor listed in each plugin but also communicate breaking changes. With that in place, we could start a campaign that:
- Communicates this QEP once approved
- Communicates the planning of the implementation
- Reminds on the effectivity of the federation

The suggested recipents would be the Dev Mailing List, the Users mailing list, and Plugins Authors.


## Backwards Compatibility

N/A

## Issue Tracking ID(s)

N/A

**LLM (AI) Assistance Disclaimer: No LLM (AI) was involved in writting this proposal.**