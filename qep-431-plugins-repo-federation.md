# QGIS Enhancement: Improve quality and federate repositories for plugins

**Date** 2026/10/14

**Author** Lova Andriarimalala (@Xpirix)

**Contact** lova at kartoza dot com

**Version** QGIS Plugins Repository Website

# Summary

We held some **discussions during the contributor meeting in Laax** to gather ideas and suggestion about how could we shape the QGIS Plugins repository facing the current issues and prevent future problems due to this exponential growth. The convergence point leans toward the federation of the doubtful plugins away to other plugins.

There are too many new plugins/versions arriving on the Plugins repo and the AI-assisted plugin has increased the volume. We went from about 3k plugins at the begining of 2026 to about 4.7k in October 2026. However, reviews are shallow and there is only one main volunteer contributor who does the review; and the qualities of plugins are at stakes with different angles:

- **Security:** A plugin can do almost anything on the user's system and a proper security review is not realistic with current resources.
- **Quality:** Some points were rised during the discussion about plugins that are forked from existing ones, plugins without open issue trackers, ones that are not easy to find in QGIS, functionality duplicated ones, non-English descripition and missing tags (AI-assisted, needs subscription, binary blobs, post-install downloads, ads, manual installation...)
- **Infrastructure pressure:** some parties harvest the whole repository every night. This eats bandwidth (and exceed the Hetzner traffic cap) and makes download counts meaningless. Ratings were open to vote-farming and have now been removed.

Earlier this year, we have implemented the QA and security checks (QEP 409) that catches real issues. However, it is more likely a tool exclusively focused on the code quality (with false positives and false negatives issues raised) without any functional checks.


## Suggested solutions

### New fields in metadata

Issued from the discussion in Laax, a suggestion of adding new fields in metadata was also raised to address quality issues:

- `monitization`: free, freemium (paid add-ons), requires subscription, contains ads
- `ai_usage`: none, assisted, generated
- `network_access`: none, optional, required
- `runtime_downloads`: true/false Does it fetch and install code or binaries after installation?
- `native_binaries`: true/false (e.g. H3)
- `ui_location`: Where to find it: menu path, toolbar, Processing provider, context menu
- `languages` / `scope`: UI languages; global or local (country/region)
- `fork_of`: Original plugin, if any

The website can store and display them first; the Desktop plugin manager can show and filter on them in a later release.


### Federated repositories

The main point is that **"if QGIS.org hosts something, people should able to trust it"** so we should make sure that the official QGIS Plugins Website only provides plugins that we can trust from upstream and federate doubtful plugins away to other repositories. From the disscussion in Laax, this could be done in three levels:

#### 1. Official QGIS Hosted plugins

This part would be the plugins that are directly hosted on the plugins.qgis.org server. The criteria of how a plugin can be hosted there has not been discussed yet. An initial critedia suggestion would:
- Combined trust on both plugin and author: even if the author has self-approve permission, any new plugin (not version) uploaded by the author should be always reviewd if it should go under the official repo or federated repo
- Strict review process and eventualy call for a vote from the PSC/Core commiters if it should be really listed here
- High quality plugin: passes all automated checks, have all necessary tags

With that in place, there will be only a few plugins that are hosted here with a badge (both on the website and the plugins manager) saying "Verified by QGIS.org".

#### 2. QGIS User Groups hosted plugins

The hosting, management, policy (paid review?), review process will be managed by a user group. QGIS.org would:
- Register the repository in admin page, 
- Initially and periodically review the repository (not all plugins)
- Harvest it nightly and publish its plugins with a trust mark, "like Mastodon federation", something like "Verified by QGIS NL"
- Repositories can be federated and de-federated

The suggestion here is to make the current "QGIS-Plugins-Website" repository as a template (or create a new repo template if necessary) that can be easily deployed. Once the new repository is registered on the official QGIS.org, the list of plugins from plugins.qgis.org will also contain the ones from the user group. However, the download links will point to the user group's server.

#### 3. Organization-hosted and self-hosted plugins

The hosting, management, policy (paid review?), review process will be managed by an organization or a person. The current repo "QGIS-Plugins-Website" can be also used to spin up the plugins' repo. Upon the organization or user's request, QGIS.org can:

- Register the repository in the admin page
- Initially and periodically review the repository (not all plugins)
- These plugins will not be listed in the plugin manager
- Only list the repo link (no harvesting and plugins list) on the plugins.qgis.org website and the way to add it to the plugin manager. We would have a badge like "Not verified" both on plugins.qgis.org and on the plugin manager.

It should be possible to consider having a "verified" Badge for these repos but it would probably require a spefic criteria, votes...

## Risks

- QGIS.org still points users at repositories that may contain plugins with low quality, we should state that federated repos goes beyond our responsability.
- It also add clicks to what is today a two-click install, which many sees as one of QGIS' best features

## Performance Implications

The federation will considerably decrease traffic on our server.

## Further Considerations/Improvements

### Communication

We recently implemented the email-verification feature which allows us to verify the correspondance of the auhtor listed in each plugin but also communicate breaking changes. With that in place, we could start a campaign that:
- Communicate this QEP once approved
- Communicate the planning of the implementation
- Reminder on the effectivity of the federation


## Backwards Compatibility

N/A

## Issue Tracking ID(s)

N/A