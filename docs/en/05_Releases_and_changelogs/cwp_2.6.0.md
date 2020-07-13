# 2.6.0

## Overview

This release includes CMS and Framework version 4.6.0.

 * [Framework 4.6.0](https://docs.silverstripe.org/en/4/changelogs/4.6.0/)

Upgrading to Recipe 2.6.0 is recommended for all CWP sites. This upgrade can be carried out by any development team familiar with Silverstripe CMS. However, if you would like Silverstripe's assistance, you can request support via the [Service Desk](https://www.cwp.govt.nz/service-desk/new-request/).


## Security considerations

This release includes  security fixes. Please see the release announcements for more detailed descriptions of each but note that the following issues have modified CVSS Environmental scores which take built-in protections from the CWP platform into account. We highly encourage upgrading your CWP projects to include these security patches nonetheless.

 * [CVE-2020-9309 Script execution on protected files](https://www.silverstripe.org/download/security-releases/CVE-2020-9309)
 * [CVE-2019-19326 Web Cache Poisoning](https://www.silverstripe.org/download/security-releases/CVE-2019-19326)
 * [CVE-2020-6164 Information disclosure on /interactive URL path](https://www.silverstripe.org/download/security-releases/CVE-2020-6164)
 * [CVE-2020-6165 Limited queries break CanViewPermissionChecker](https://www.silverstripe.org/download/security-releases/CVE-2020-6165)


## New features

The [release announcement](https://www.cwp.govt.nz/updates/news/announcing-cwp-2-6) includes the note worthy features, but be sure to review the change log for full detail.


### PHP 7.4 Compatibility

PHP 7.4 is commercially supported from CWP 2.6.0 (CMS 4.6.0) onwards. All new CWP stacks will include PHP 7.4 by default.
To have an existing CWP stack upgraded to PHP 7.4 please get in touch with the [CWP Service Desk](https://www.cwp.govt.nz/footer-holder/contact/)


### Mimevalidator is now a part of silverstripe/recipe-core

For most CWP installations this should be a transparent change. However, if you changed the mimevalidator configuration,
a manual step is required when upgrading to CWP 2.6.0.

As a result of [RFC 8782](https://github.com/silverstripe/silverstripe-framework/issues/8782),
the module `silverstripe/mimevalidator` is now included into `silverstripe/recipe-core`,
which is a dependency of `cwp/cwp-recipe-core`.

Historically, `silverstripe/mimevalidator` was a dependency of `cwp/cwp-recipe-core < 2.6` and
`cwp/cwp < 2.6` contained configuration for that module (in [vendor/cwp/cwp/_config/mimevalidator.yml](https://github.com/silverstripe/cwp/blob/2.5.2/_config/mimevalidator.yml)).

After the upgrade, your project will get a new configuration file at [app/_config/mimevalidator.yml](https://github.com/silverstripe/recipe-core/blob/4.6.0/app/_config/mimevalidator.yml),
which is compatible with the pre-existing configuration file. However, if you have introduced any custom
settings for mimevalidator, the new file may override it by accident. We recommend either
moving your configuration into the new file, or deleting the new file so it does not interfere.

If you do not have any custom settings for the mimevalidator module, no action is required.

If you have opted-out of `silverstripe/mimevalidator` module by manually removing it from your composer.json,
you should consider disabling it via configuration, since it will be automatically reinstalled when upgrading to 2.6.0.

For more information see:
 - [RFC 8782](https://github.com/silverstripe/silverstripe-framework/issues/8782)
 - Silverstripe CMS [4.6.0 changelog](https://docs.silverstripe.org/en/4/changelogs/4.6.0/#mime-validator) explaining the impact
 - [silverstripe/mimevalidator](https://github.com/silverstripe/silverstripe-mimevalidator/) module documentation
 - Silverstripe CMS [documentation](https://docs.silverstripe.org/en/4/developer_guides/files/allowed_file_types/#mime-type-validation) about mime type validation


### Multi-factor authentication (MFA) modules become a part of the default CWP installation

The following modules are now included into [cwp/installer](https://github.com/silverstripe/cwp-installer)
 * [silverstripe/mfa](https://github.com/silverstripe/silverstripe-mfa/)
 * [silverstripe/totp-authenticator](https://github.com/silverstripe/silverstripe-totp-authenticator/)

All new projects starting with the recipe of version 2.6.0 will have MFA support included.
Common Web Platform (CWP infrastructure) has the support for it already, so no extra setup required.

Since the modules become a part of [cwp/installer](https://github.com/silverstripe/cwp-installer), the change will only affect
new projects. An upgrade to 2.6.0 will not install the MFA modules. However, manual installation is as easy as `composer require`.

### Solr no longer indexes draft / restricted content

This CWP release includes an update to the [fulltextsearch module](https://github.com/silverstripe/silverstripe-fulltextsearch) to introduce more secure defaults. Most notably, **draft and restricted content will no longer be indexed by default**, due to a `canView()` check being performed against an anonymous user prior to (re)indexing.  Restricted content means that it has a permission level of either `Logged-in users` or `Only these groups`.

After upgrading your website, ensure that you run the `Solr_Reindex` task on your production environment to remove previously indexed content that should no longer be there.

If your website requires draft or restricted content to be indexed, you can [opt-out](https://github.com/silverstripe/silverstripe-fulltextsearch/blob/3/README.md#important-note-when-upgrading-to-fulltextsearch-37) of the new secure defaults on a per-model basis.

This is a great opportunity to make sure that any custom indexes / search controllers in your project are correctly filtering results based on permissions and search visibility, which you can now achieve via a unified method (see `SilverStripe\FullTextSearch\Search\ServicesSearchableService::isSearchable()`.)

### Subsites is no longer included in the default CWP installation

The [Subsites module](https://github.com/silverstripe/silverstripe-subsites) has been removed from
[cwp/installer](https://github.com/silverstripe/cwp-installer) in 2.6.0, in order to reduce the
default technical footprint of the recipe. Many sites operate without need of Subsites, and we feel
that including this module should be left as an explicit decision for developers to make.

New projects that need Subsites will need to manually install it via Composer:

```
composer require silverstripe/subsites ^2.3
```

We will also no longer reference it in the Upgrading instructions section. If your site depends on
Subsites, ensure you continue to include and update the module in your Composer requirements.

## Known issues

There are no known CWP issues in this release.

### Expected test failures

There are no known PHPUnit test failures in this release.

## Upgrading instructions

In order to update an existing site to use the new CWP recipe the following changes to your composer.json can be made:

```
"require": {
    "cwp/cwp-recipe-core": "2.6.0@stable",
    "cwp/cwp-recipe-cms": "2.6.0@stable",
    "silverstripe/recipe-blog": "1.6.0@stable",
    "silverstripe/recipe-form-building": "1.6.0@stable",
    "silverstripe/recipe-authoring-tools": "1.6.0@stable",
    "silverstripe/recipe-collaboration": "1.6.0@stable",
    "silverstripe/recipe-reporting-tools": "1.6.0@stable",
    "cwp/cwp-recipe-search": "2.6.0@stable",
    "silverstripe/recipe-services": "1.6.0@stable",
    "tractorcow/silverstripe-fluent": "4.5.0@stable",
    "silverstripe/registry": "2.2.1@stable",
    "cwp/starter-theme": "3.0.4@stable"
},
"prefer-stable": true
```

<!--- Changes below this line will be automatically regenerated -->

## Change Log

### Security

 * 2020-05-11 [d459c51](https://github.com/silverstripe/silverstripe-userforms/commit/d459c51adae678de2477c3fd7c6f115ece4962db) Require MimeUploadValidator on userformis' File Upload field (Maxime Rainville) - See [cve-2020-9309](https://www.silverstripe.org/download/security-releases/cve-2020-9309)
 * 2020-05-13 [996c1b571](https://github.com/silverstripe/silverstripe-framework/commit/996c1b57195029ef2d385123e22a3222cd7e5f18) Remove/deprecate unused controllers that can potentially give away some information about the underlying project. (Maxime Rainville) - See [cve-2020-6164](https://www.silverstripe.org/download/security-releases/cve-2020-6164)
 * 2020-05-11 [71db45b18](https://github.com/silverstripe/silverstripe-framework/commit/71db45b18b4a3bce6a4630ff3a6c116c15f35874) Stop honouring X-HTTP-Method-Override header, X-Original-Url header and _method POST variable. Add SS_HTTPRequest::setHttpMethod() (Maxime Rainville) - See [cve-2019-19326](https://www.silverstripe.org/download/security-releases/cve-2019-19326)
 * 2020-02-17 [6c3a619](https://github.com/silverstripe/silverstripe-asset-admin/commit/6c3a6197e5bd5bb8f45e007338b4c2c3b4f3a6c6) Move the query resolution after the DataListQuery has been altered (Maxime Rainville) - See [cve-2020-6165](https://www.silverstripe.org/download/security-releases/cve-2020-6165)
 * 2020-02-11 [044eb43](https://github.com/silverstripe/silverstripe-graphql/commit/044eb43ad02428b17a881e47a0ff8aa8d159eb9c) Ensure canView() check is run on items (Steve Boyd) - See [cve-2020-6165](https://www.silverstripe.org/download/security-releases/cve-2020-6165)

### API Changes

 * 2020-05-08 [bf31e89](https://github.com/silverstripe/cwp-recipe-core/commit/bf31e89e1737b8ad2c9026bd81cdb39f0f897837) Remove explicit requirement for silverstripe/mimevalidator (Maxime Rainville)
 * 2020-05-08 [d603599](https://github.com/silverstripe/cwp/commit/d60359909214d7f0d5f5f5747768b465372cd50c) Remove mimevalidator from explicit CWP requirements (Maxime Rainville)
 * 2020-01-12 [793f437](https://github.com/silverstripe/silverstripe-userforms/commit/793f43728947b7a90284e47f82efd2df5464c914) Mark EditableFormField::EffectiveDisplayRules() for deprecation (Maxime Rainville)

### Features and Enhancements

 * 2020-06-10 [cfe937f](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/cfe937fbd1c4103ed934051ab20d56f7467445ee) canView() check on record before indexing and before showing in search results (Steve Boyd)
 * 2020-06-10 [3b40be3](https://github.com/silverstripe/silverstripe-userforms/commit/3b40be3b8f0455591f658fa1dbda9852e55c9bcc) Add folder confirmation modal when adding a new `EditableFileField` to a userform. (#972) (Andre Kiste)
 * 2020-06-09 [14ce51e](https://github.com/silverstripe/silverstripe-userforms/commit/14ce51e517f5d994cb1b270fde1f41f65cf3d68b) Improve Unrestricted Folder warning, bump core requirement to ^4.6 (#965) (Garion Herman)
 * 2020-05-28 [ed53709](https://github.com/silverstripe/silverstripe-userforms/commit/ed53709631261c1e2eeab6c83a39a2f9f0e03598) DB column showing if file is userform upload (Steve Boyd)
 * 2020-04-24 [e582a92](https://github.com/silverstripe/cwp-installer/commit/e582a9283e9f49575ff6b2d7bf3151da34face9c) remove subsites from cwp-installer (brynwhyman)
 * 2020-03-03 [431b6c7](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/431b6c7dd9a0bb7b41066df6ac42fc04334035de) Update to RealMe 4.x, drop PHP 7.1 from authenticator tests (Garion Herman)
 * 2020-02-17 [2b3f986](https://github.com/silverstripe/silverstripe-login-forms/commit/2b3f98626765693be32a20f3a36aa5765cf4ae25) Make dark mode support optional (Elliot Sawyer)
 * 2020-01-13 [81d466e](https://github.com/silverstripe/silverstripe-realme/commit/81d466e61ed9d7ee7ffb27fae448f4bf5ceb7586) support JSON encoded identity data for ASSERT (Dylan Wagstaff)
 * 2019-08-05 [9c9b2a3](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/9c9b2a3e2046f2c73b2582993f19db471e6c7ce9) Add extension point to appliesTo for SearchVariantSubsites (Scott Hutchinson)
 * 2019-07-23 [f2e3db6](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/f2e3db6586daae035f7371be00fe63b85e7f7a35) Bump minimum PHP to 7.1, SilverStripe to 3.7, replace PHP 7.2 incompatible code (Robbie Averill)
 * 2019-05-30 [566ffa0](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/566ffa0593d68c5f0a96db452d3a55c3382d57a7) Disable re-indexing when migrating files (Maxime Rainville)

### Bugfixes

 * 2020-06-11 [dc5ce00](https://github.com/silverstripe/silverstripe-userforms/commit/dc5ce0000a9f9e52523b94cb21ad596b2c98980c) Fix confirm upload form modal on Elemental block (Maxime Rainville)
 * 2020-05-27 [c3d990f](https://github.com/silverstripe/silverstripe-userforms/commit/c3d990f70fc76c2f7e1203fa948ecb56d0956b3a) Create image thumbnails for asset-admin for images uploaded through userforms (#969) (Steve Boyd)
 * 2020-05-06 [c538a23](https://github.com/silverstripe/silverstripe-tagfield/commit/c538a231b709e36f2971aec7971e041151685ac1) $record-&gt;hasMethod() should be called before getting the relation (Christopher Darling)
 * 2020-05-04 [6276e99](https://github.com/silverstripe/silverstripe-userforms/commit/6276e990c1b7ffef69bb74cc2a6a2c26a6e53e53) Better anchor fallback (Mojmir Fendek)
 * 2020-05-04 [5d739c4](https://github.com/silverstripe/silverstripe-userforms/commit/5d739c4db4f9c8d190a5b4f8e38fa2e57eb323c2) Add missing space before aria-describedby attribute (Michal Kleiner)
 * 2020-04-28 [3673000](https://github.com/silverstripe/cwp-search/commit/3673000a6c7e3cce6482db1dac0bebc8e9bcb793) Use IndexableService to filter search results (Steve Boyd)
 * 2020-04-27 [bb657d1](https://github.com/silverstripe/silverstripe-securityreport/commit/bb657d1bd000d05b61cbb9ff1bbe635e0f39d1fd) Fix description of UserSecurityReport (Matt Peel)
 * 2020-04-22 [273cdd8](https://github.com/silverstripe/silverstripe-mfa/commit/273cdd825c17ad76c30ba64fb6960670b8a9af5b) Ensure canSkipMFA respects whether MFA is enabled (Garion Herman)
 * 2020-04-17 [5f6aac8](https://github.com/silverstripe/silverstripe-mfa/commit/5f6aac8e4e3838ff16dfbaa49879a34f65d3d23e) Remove redundant Config nesting in EnforcementManagerTest (Garion Herman)
 * 2020-04-17 [cf9c25e](https://github.com/silverstripe/silverstripe-mfa/commit/cf9c25e551e4f593e20cd1b21c98d39e92e92bcc) Add code coverage for EnforcementManager::hasCompletedRegistration (Garion Herman)
 * 2020-04-17 [33e29d9](https://github.com/silverstripe/silverstripe-mfa/commit/33e29d977c342dc0124a0fb2d2a5c9f4ac9aa433) Increase code coverage, correct logic in grace period tests (Garion Herman)
 * 2020-04-16 [38962a5](https://github.com/silverstripe/silverstripe-mfa/commit/38962a5d477c536565d82e2798e04bb59a7b358f) Check whether user is eligible for MFA in canSkipMFA method (Garion Herman)
 * 2020-03-26 [56dae9c](https://github.com/silverstripe/silverstripe-taxonomy/commit/56dae9c28b2ee2fe57d936dabbef8ed14662c370) fix codsniffer complaint (Werner M. Krauß)
 * 2020-03-23 [a648e63](https://github.com/silverstripe/silverstripe-mfa/commit/a648e634f34bf27012a423f942f64356b11b30fd) Members minimal CMS access were previously stuck in a login loop, now redirects to MFA (Robbie Averill)
 * 2020-03-22 [8e028a7](https://github.com/silverstripe/silverstripe-security-extensions/commit/8e028a76806d39a3d67fad77a802f5d3a5d1a389) linting errors (brynwhyman)
 * 2020-03-16 [5e2c9db](https://github.com/silverstripe/silverstripe-login-forms/commit/5e2c9db13913aca51e149fbfc088d343e8588155) Use `.silverstripe-cms-logo` in place of `svg` to target logo (Garion Herman)
 * 2020-03-12 [b05cdaa](https://github.com/silverstripe/silverstripe-security-extensions/commit/b05cdaa041fb275c5b6574dcaddae7f555672c71) point sudomode help link to userhelp not google (brynwhyman)
 * 2020-03-09 [71a8cac](https://github.com/silverstripe/silverstripe-userforms/commit/71a8cac9e5b3ca99fa3fa51ee42c045f6c5bedb5) Remove bad default value for UserForm Content (#926) (Maxime Rainville)
 * 2020-03-05 [17749ac](https://github.com/bringyourownideas/silverstripe-composer-security-checker/commit/17749acff0a334d5ac845e973c765176826ce629) Instantiate SecurityAlertCheckTask correctly in Job (Garion Herman)
 * 2020-02-20 [c45ae77](https://github.com/silverstripe/silverstripe-realme/commit/c45ae7766afd13a3f709797ab7bb60f1ab2c2a03) bad array index lookup for attributes (Dylan Wagstaff)
 * 2020-02-10 [d759a78](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/d759a78182eb10673f81cd964df7c6677c411940) allow update processor to be overriden by existing yml (Naomi Guyer)
 * 2020-02-10 [957037f](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/957037fc433f06472152dd61876c79be57370c8e) SearchUpdateQueuedJobProcessor JobType NULL (Naomi Guyer)
 * 2020-02-05 [d844ac2](https://github.com/silverstripe/cwp-core/commit/d844ac29c6a3ae02c3407194a8cb604f5b5b0a4e) Add resetaccount route to basic auth exceptions (Garion Herman)
 * 2020-01-16 [8a3237f](https://github.com/silverstripe/silverstripe-realme/commit/8a3237f4d19561ae6f670e72565a99d1802a10e4) do not sync data with transient IDs (Dylan Wagstaff)
 * 2020-01-12 [c01e8be](https://github.com/silverstripe/silverstripe-realme/commit/c01e8be6f2e23ffb3142d0b1530fc0268629c750) undefined variable dereference inside catch block (Dylan Wagstaff)
 * 2020-01-07 [cd52ed1](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/cd52ed1f4db3abbd0eb70075056b340ae965f5b3) Fix block link fields in non inline editing (Hayden Shaw)
 * 2019-12-16 [b9401fb](https://github.com/silverstripe/cwp/commit/b9401fb8f4e9a946d438f8c86b01dfc3394547ad) Fix link to source code in docs (elliot sawyer)
 * 2019-12-15 [42cc545](https://github.com/silverstripe/silverstripe-textextraction/commit/42cc5454146f7ca0d17521a8477f6e62a1685ea6) Fixes #58 We always want $content (an array) passed to implode() (Russell Michell)
 * 2019-12-13 [5b5ebfd](https://github.com/silverstripe/cwp-agencyextensions/commit/5b5ebfde131c1b8e8c9742f3871da618f6c8d1cc) fix allowed elements and element-attributes in CMS (Jakub Dolba)
 * 2019-12-04 [34f5255](https://github.com/silverstripe/cwp/commit/34f5255316681f7754279954553a05b662ac07b4) WR325373 guard for invalid dates on datedupdatecontroller (Elliot Sawyer)
 * 2019-11-26 [a38ee6c](https://github.com/silverstripe/silverstripe-security-extensions/commit/a38ee6c4737cac5465ab39e8dd2940f0101a3fdf) Reinstate PHPCS build, fix linting issues (Garion Herman)
 * 2019-11-20 [640cb3d](https://github.com/silverstripe/silverstripe-mfa/commit/640cb3d115757e7ce289458e26af828ae333c662) Expose the client/lang directory so we can load JS side tran… (#368) (Guy Marriott)
 * 2019-11-20 [4b97095](https://github.com/silverstripe/silverstripe-mfa/commit/4b97095141b58d3fc29efdfc0131e00a9e558a20) Expose the client/lang directory so we can load JS side translation properly. (Maxime Rainville)
 * 2019-08-27 [d41d27e](https://github.com/silverstripe/silverstripe-realme/commit/d41d27eda5417651697c3c829f288a027d8dd58e) Update to be PHP 7.2+ compatible (Dylan Wagstaff)
 * 2019-08-26 [9a1c9ab](https://github.com/silverstripe/cwp-core/commit/9a1c9ab6905c5976e0cb76ad1d5dfa00154408ac) Fix #74 textextraction config to enable it after 'textextractionconfig' defined in silverstripe/silverstripe-textextraction as well as using the use file caching as intended. (Charlie Bergthaler)
 * 2019-08-12 [a724dc7](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/a724dc7117b536c6796bc84ea03551b7b04c6ffa) Restore PHP 5.6 support (Robbie Averill)
 * 2019-07-15 [fbfead9](https://github.com/silverstripe/silverstripe-comments/commit/fbfead9c9ed83581780c7cfb0cd5c2e4b780e100) CommentAdmin implements PermissionProvider (Jason Irish)
 * 2019-07-09 [223d260](https://github.com/silverstripe/silverstripe-realme/commit/223d2606f92f4e80145855155e81095b4e7d5a40) Fix overseas RealMe contact phone (Indy Griffiths)
 * 2018-11-30 [82d0bb4](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/82d0bb4efe601fdcedaadd2ca8020676e9397032) #108 for SS 3.x - Update and simplify getDirtyIDs method (Michal Kleiner)
 * 2018-11-30 [167597d](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/167597db7bcfb4adaf03234d4c9ca676b396d023) #108 - Update and simplify getDirtyIDs method (Michal Kleiner)

### Other changes

 * 2020-07-01 [49fa1a5](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/49fa1a5f82fe5a53d826bc0f437a012d48bf75c6) Remove mcrypt from dependencies (Maxime Rainville)
 * 2020-07-01 [3f758a3](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/3f758a318d83a10d9b80996be553c8aafe757539) Target framework 4.x-dev on the sink (#71) (Maxime Rainville)
 * 2020-06-21 [3862afd](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/3862afd15f20329797a7a12f0301d7dc9ed0cbd4) Remove MFA, add Subsites (Steve Boyd)
 * 2020-06-16 [67e4f4d](https://github.com/silverstripe/cwp/commit/67e4f4ded7bc803589ab9dcde2993da06783b8e5) Tweak the 2.6.0-rc1 changelog (Maxime Rainville)
 * 2020-06-16 [887e8ba](https://github.com/tractorcow-farm/silverstripe-fluent/commit/887e8ba7e70fb41069294f62a2bb76090b2eea9d) Update translations (Maxime Rainville)
 * 2020-06-16 [71cb55e](https://github.com/silverstripe/silverstripe-mfa/commit/71cb55e3715445396afe54bac080e055bdfd1186) Update translations (Maxime Rainville)
 * 2020-06-16 [6c2374a](https://github.com/silverstripe/cwp-core/commit/6c2374aca8e2f576654bcc54bafe224c572f29cd) Update translations (Maxime Rainville)
 * 2020-06-16 [ee9af65](https://github.com/silverstripe/silverstripe-login-forms/commit/ee9af65f1269b3788299fb2e1eefe766a400ee77) Update translations (Maxime Rainville)
 * 2020-06-16 [c0c4698](https://github.com/silverstripe/silverstripe-userforms/commit/c0c46981de333adfd3f99c315673a41bea49f12b) Update translations (Maxime Rainville)
 * 2020-06-16 [a770e09](https://github.com/silverstripe/silverstripe-taxonomy/commit/a770e0907c5078a43890c069d034079274e83a98) Update translations (Maxime Rainville)
 * 2020-06-16 [754cedd](https://github.com/symbiote/silverstripe-advancedworkflow/commit/754cedd26ca69eff76716388188e0fc2245a6fb3) Update translations (Maxime Rainville)
 * 2020-06-16 [ba1f52c](https://github.com/silverstripe/silverstripe-securityreport/commit/ba1f52c0f36fd2b0b30fb78a30c01b2c1bec5ec1) Update translations (Maxime Rainville)
 * 2020-06-15 [df47884](https://github.com/silverstripe/cwp/commit/df47884095889c0afa87e3deef8b3a06033dcf74) DOC Filtering of records on canView() and ShowInSearch (#266) (Steve Boyd)
 * 2020-06-15 [b2625d9](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/b2625d97d2f036042a44b4e0a551a13799a82f5e) Update for 2.6 (#68) (Steve Boyd)
 * 2020-06-15 [e521305](https://github.com/silverstripe/cwp/commit/e5213052c654948e36b28a419bfe9a0c57661844) Update for 2.6 (#276) (Steve Boyd)
 * 2020-06-15 [e550db2](https://github.com/silverstripe/cwp-recipe-cms/commit/e550db286bc5adf143162b976e78034e5e852337) Update for 2.6 (#15) (Steve Boyd)
 * 2020-06-15 [66ddb9d](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/66ddb9d0aba8016c5fd15c8187b6367587fb9b2c) Update for 2.1 (#45) (Steve Boyd)
 * 2020-06-15 [8477674](https://github.com/silverstripe/silverstripe-login-forms/commit/84776747517a98db0962f745c654f69198dd27e2) Update for 4.2 (#59) (Steve Boyd)
 * 2020-06-15 [e51ccd8](https://github.com/silverstripe/cwp-recipe-core/commit/e51ccd883abf13b8ce595ef9d71f1c1828b6682d) Update for 2.6 (#15) (Steve Boyd)
 * 2020-06-15 [e401d69](https://github.com/silverstripe/cwp-search/commit/e401d695258f3e7b2e184cc757c9a8992dfa9d43) Set up 1.3 branch (Maxime Rainville)
 * 2020-06-15 [203127a](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/203127a68424d219c72362be2ab0e606118fcf5e) Set up 3.7 branch (Maxime Rainville)
 * 2020-06-15 [5940d77](https://github.com/silverstripe/recipe-authoring-tools/commit/5940d77fb02e00bbf52217ddbd12536ca8c90b21) Update for 1.6 (#9) (Steve Boyd)
 * 2020-06-15 [8666f1d](https://github.com/silverstripe/recipe-blog/commit/8666f1d46ee30dd097a34aaf1fbe9ea419efc284) Update for 1.6 (#19) (Steve Boyd)
 * 2020-06-15 [c3cf445](https://github.com/silverstripe/recipe-collaboration/commit/c3cf4453d88669e20ecf0cd9b649a05bb3b98d37) Update for 1.6 (#8) (Steve Boyd)
 * 2020-06-15 [44eec6e](https://github.com/silverstripe/recipe-content-blocks/commit/44eec6e08a509a7a697e213ca85c690409204900) Update for 2.6 (#12) (Steve Boyd)
 * 2020-06-15 [03b3265](https://github.com/silverstripe/silverstripe-userforms/commit/03b32659ca9a7001e39848f168faa91fe0f5384f) Update for 5.6 (#978) (Steve Boyd)
 * 2020-06-15 [848b434](https://github.com/silverstripe/cwp-core/commit/848b4343533e679cf4a600c39e47c47a9893a815) Update for 2.6 (#87) (Steve Boyd)
 * 2020-06-15 [e10dc2e](https://github.com/silverstripe/cwp-recipe-search/commit/e10dc2e33bcf425b36423173f0f0f4858b0c7b2b) Tweak targeted version (Maxime Rainville)
 * 2020-06-15 [7088895](https://github.com/silverstripe/cwp-installer/commit/70888951f5299abefd54ac0fcb1190179d51f9c5) Update for 2.6 (Steve Boyd)
 * 2020-06-15 [2a13dc6](https://github.com/silverstripe/recipe-form-building/commit/2a13dc68a2b1c44116c5afb420d23606305de923) Set up 1.6 branch (Maxime Rainville)
 * 2020-06-15 [c20d288](https://github.com/silverstripe/recipe-reporting-tools/commit/c20d288bf13461900cbb3e8d76cf3e0f9feba144) Set 1.6 branch (Maxime Rainville)
 * 2020-06-15 [c1dfc50](https://github.com/silverstripe/cwp-recipe-search/commit/c1dfc50fd7eecf5a4dff0e7738a224d3b5ccc12a) Update for 2.6 (Steve Boyd)
 * 2020-06-15 [2a034de](https://github.com/silverstripe/recipe-authoring-tools/commit/2a034def5796c4826d1380ff48009c7b581172ef) Set up build for 1.6 branch (Maxime Rainville)
 * 2020-06-15 [f95df01](https://github.com/silverstripe/cwp-agencyextensions/commit/f95df01d605863f0a683a3282784924de6bce9f2) Update for 2.4 (Steve Boyd)
 * 2020-06-15 [5fa29f0](https://github.com/symbiote/silverstripe-advancedworkflow/commit/5fa29f01039128b2dc433ee5530628833a5b16ca) Update for 5.3 (Steve Boyd)
 * 2020-06-15 [e451f96](https://github.com/silverstripe/silverstripe-textextraction/commit/e451f96b0bcd0ff066a43a2722980f49c1f1478b) Update for 3.1 (Steve Boyd)
 * 2020-06-15 [a9a136a](https://github.com/silverstripe/silverstripe-taxonomy/commit/a9a136af17e334ac0036d12c1698b8705c2ecdf9) Update for 2.1 (Steve Boyd)
 * 2020-06-15 [dfd9c15](https://github.com/silverstripe/silverstripe-environmentcheck/commit/dfd9c15d5628bc16675bac105330dce013405700) Add PHP 7.4 test (Maxime Rainville)
 * 2020-06-15 [8b576c6](https://github.com/silverstripe/silverstripe-securityreport/commit/8b576c68b861757f9ea1f5e174e017caff7f4cb4) Update for 2.2 (Steve Boyd)
 * 2020-06-15 [612bf61](https://github.com/silverstripe/silverstripe-mfa/commit/612bf6195f21123be838ce1c41c27053e1fd7606) Update for 4.1 (Steve Boyd)
 * 2020-06-15 [1aa18e7](https://github.com/silverstripe/silverstripe-environmentcheck/commit/1aa18e7ceda2f90db90ab69c3ad3a41fd51e208b) Update for 2.2 (Steve Boyd)
 * 2020-06-12 [d44c82a](https://github.com/silverstripe/cwp-recipe-cms/commit/d44c82af33d1bb057ccab16295f33b13eb5c3484) Revert "Update Composer / Travis config to CMS 4.5 / CWP 2.5 series" (Maxime Rainville)
 * 2020-06-11 [25978d3](https://github.com/silverstripe/silverstripe-userforms/commit/25978d3c583856f5bd023fdd101a1dad2ed6738a) Clean up tests (Maxime Rainville)
 * 2020-06-11 [df12013](https://github.com/silverstripe/silverstripe-userforms/commit/df120133abfcf68b4fa54343276d7380ef877b8b) Move confirm folder logic to a dedicated controller (Maxime Rainville)
 * 2020-06-10 [28dcc0b](https://github.com/silverstripe/cwp/commit/28dcc0b1cdc990a93d99dcd1c6578b8186649ff8) Changelog for 2.6.0 (Serge Latyntcev)
 * 2020-06-09 [f961d88](https://github.com/silverstripe/silverstripe-mfa/commit/f961d884923b9a8e0411b81c7ecb81bef20c4bed) Bump npm from 6.9.0 to 6.14.5 (dependabot[bot])
 * 2020-06-09 [64ba3da](https://github.com/silverstripe/silverstripe-mfa/commit/64ba3dab1e06666406670993977feefffe6e68e3) Bump lodash from 4.17.11 to 4.17.15 (dependabot[bot])
 * 2020-06-09 [7e395cf](https://github.com/silverstripe/cwp-installer/commit/7e395cfd78a283cb70c28aa806980ca0268e9235) Add mfa (bergice)
 * 2020-06-03 [83a1ebd](https://github.com/silverstripe/silverstripe-mfa/commit/83a1ebddf50b2a6dc277f5ac0bac296860079c63) DOCS Update naming convention from 'SilverStripe' to 'Silverstripe' as per brand change (Sacha Judd)
 * 2020-06-03 [a57863a](https://github.com/silverstripe/silverstripe-mfa/commit/a57863a3d5eb73c4dc11cd1f3ee6ff21486bec8e) Bump handlebars from 4.1.2 to 4.7.6 (dependabot[bot])
 * 2020-06-03 [50af435](https://github.com/silverstripe/silverstripe-mfa/commit/50af435f080e892f7687760d1b5bf7ee0bd6c72a) Bump https-proxy-agent from 2.2.1 to 2.2.4 (dependabot[bot])
 * 2020-06-03 [99fe77a](https://github.com/silverstripe/silverstripe-mfa/commit/99fe77a6497ee0ed09791e3a491de4fd74c562cf) Bump mixin-deep from 1.3.1 to 1.3.2 (dependabot[bot])
 * 2020-06-03 [053c070](https://github.com/silverstripe/silverstripe-mfa/commit/053c070a5384b1ee04a3c50549c9a3d6f0b742a8) DOCS feedback on MFA userhelp (brynwhyman)
 * 2020-05-28 [079101c](https://github.com/silverstripe/silverstripe-mfa/commit/079101ce9c840920f33a4a53239352912779bbab) DOCS userhelp section for trusted devices (brynwhyman)
 * 2020-05-27 [273f70d](https://github.com/silverstripe/silverstripe-login-forms/commit/273f70df8526ab9b4fd9075e7df80d54caddfb96) DOC Clean up login-forms README (Maxime Rainville)
 * 2020-05-26 [f27045b](https://github.com/silverstripe/silverstripe-userforms/commit/f27045ba93fc656d184ec3d25dfbfde29f3119cd) DOC Recommend installation of spamprotection (#966) (Maxime Rainville)
 * 2020-05-25 [1de9ca3](https://github.com/silverstripe/cwp/commit/1de9ca37eb0afb9264f15ba873eaefba2ecc3455) Revert "API Remove mimevalidator from explicit CWP requirements" (#270) (Maxime Rainville)
 * 2020-05-05 [e86cc8d](https://github.com/silverstripe/silverstripe-userforms/commit/e86cc8d8721b1372aca4f4f8f0b74398fae909c6) Add lint and lint-clean scripts to composer (Dan Hensby)
 * 2020-05-05 [ede2d93](https://github.com/silverstripe/silverstripe-userforms/commit/ede2d933631300d700a3207a1b61bbd69e80b1f2) Linting fixes (Dan Hensby)
 * 2020-05-03 [7adc42f](https://github.com/silverstripe/silverstripe-userforms/commit/7adc42f6b8e50dfe8ca64258192c14c4cacab9a4) update docs (Lukas)
 * 2020-04-30 [fa5005d](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/fa5005d13b0db06107137b697e3789c9b90be870) Bump jquery from 3.4.0 to 3.5.0 (dependabot[bot])
 * 2020-04-30 [d508f21](https://github.com/silverstripe/cwp-agencyextensions/commit/d508f21ce4637a8ed2b600a1de17e148e841ee16) Bump jquery from 3.4.1 to 3.5.0 (dependabot[bot])
 * 2020-04-30 [9543480](https://github.com/silverstripe/silverstripe-userforms/commit/9543480ce8ff584772e182990acea6c2157925a7) Bump jquery from 3.4.1 to 3.5.0 (dependabot[bot])
 * 2020-04-24 [03ac2a6](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/03ac2a679e2453f069092c35f964aa4ec21bdb80) Reorder recipe-cms test suite to bypass a silly problem with SearchFormTest (Maxime Rainville)
 * 2020-04-20 [ac053b5](https://github.com/silverstripe/silverstripe-akismet/commit/ac053b5800eadb45b767b3b50d6a0ae44e79a512) MINOR: Account for undocumented exception in Akismet::getIsSpam (#31) (elliot sawyer)
 * 2020-04-19 [1b3357d](https://github.com/silverstripe/cwp/commit/1b3357dfcb6886fc919e1c72bd7541209b931fdb) DOC fix changelog for 2.5.2 (Serge Latyntcev)
 * 2020-04-19 [4583d9b](https://github.com/silverstripe/cwp/commit/4583d9b29be367edda107964261d9e4c2f9cf4ce) DOC fix changelog for 2.4.1 (Serge Latyntcev)
 * 2020-04-19 [98e35ff](https://github.com/silverstripe/cwp/commit/98e35ff315cef7207a82a09522c0fbb2495c05ec) DOC fix changelog for 2.3.3 (Serge Latyntcev)
 * 2020-04-18 [254cc71](https://github.com/silverstripe/cwp-installer/commit/254cc71af5619defca4b12b43d8dbeff42706d3d) Use 2.x-dev (Steve Boyd)
 * 2020-04-16 [62f453d](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/62f453d89b13ad00c56508c382b4180ececd1adc) Bump https-proxy-agent from 2.2.1 to 2.2.4 (dependabot[bot])
 * 2020-04-15 [4b7f99a](https://github.com/silverstripe/cwp/commit/4b7f99a4421fbb948e38c5b92ac0bc61d0f2980f) DOC add releases 2.3.3, 2.4.1 and 2.5.2 to the changelog index (Serge Latyntcev)
 * 2020-04-15 [908d03b](https://github.com/silverstripe/cwp/commit/908d03ba313e5a61e32c344e90ce6cc0fbcdfe69) DOC fix changelogs for 2.4.1 (Serge Latyntcev)
 * 2020-04-14 [7772488](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/77724882dcad85a4f7737816b7993b83d7c0f6cb) Update PHP to 7.1 (Serge Latyntcev)
 * 2020-04-09 [f28bb61](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/f28bb610129787c145151b44eb2d9c0bf153c917) Pin the PHP version and mcrypt extension requirements (Serge Latyntcev)
 * 2020-04-08 [bfa2ede](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/bfa2edee6a7071d8cac7c58dd5d78745b48c9dec) Improved search filtering based on visibility (Steve Boyd)
 * 2020-04-08 [314cff6](https://github.com/symbiote/silverstripe-advancedworkflow/commit/314cff6a49dbfa202bec966110fa2d9274f9299e) Update fields containing tags to use `DBHTMLText` (shoosah)
 * 2020-04-08 [7e6d36d](https://github.com/tractorcow-farm/silverstripe-fluent/commit/7e6d36d16b2dc2ee1ba437440bbca23648e73cec) Prevent duplicate siteconfigs when localised (Damian Mooyman)
 * 2020-04-08 [95a12d4](https://github.com/tractorcow-farm/silverstripe-fluent/commit/95a12d46ca9b6289cf989bff21defe910eea5858) Revert "Latest version issue fix" (Damian Mooyman)
 * 2020-04-07 [468f939](https://github.com/symbiote/silverstripe-advancedworkflow/commit/468f9397a551f69bc02b8690007b79b8159db336) Update src/DataObjects/WorkflowDefinition.php (Sherry Shahbazi)
 * 2020-04-07 [a77b358](https://github.com/symbiote/silverstripe-advancedworkflow/commit/a77b358bbcb23a7e42c2fc9cdc968ac05a5d926d) Update travis config. Remove &lt;=7.0 php tests and add 7.4 php test (Dan Hensby)
 * 2020-04-07 [e20545d](https://github.com/symbiote/silverstripe-advancedworkflow/commit/e20545d5e8f0bbf857cf9c905054bb398bf61d37) Pass arguments with preg_quote in the regex function (shoosah)
 * 2020-04-06 [92d0987](https://github.com/silverstripe/silverstripe-mfa/commit/92d0987b71d8d92b90949d63623fd0faf3d49932) Remove admin_email constraint for password reset email (Amol Wankhede)
 * 2020-04-01 [d30299f](https://github.com/bringyourownideas/silverstripe-composer-security-checker/commit/d30299f54ced5d36ec84eda9382ec626f2353466) Loosen security checker constraint (Sheila Bañez)
 * 2020-04-01 [b51e5e0](https://github.com/bringyourownideas/silverstripe-composer-security-checker/commit/b51e5e0806cc1bd5fd4acefe9f80fd91ebb2a25b) Upgrade sensiolabs/security-checker from 5 to 6 (Sheila Bañez)
 * 2020-03-26 [4280408](https://github.com/silverstripe/silverstripe-taxonomy/commit/4280408cdc9a036dc88e2ca1560543b726c552b8) child term should take type from parent item (Werner M. Krauß)
 * 2020-03-26 [03c3190](https://github.com/silverstripe/silverstripe-taxonomy/commit/03c3190347bd953dccc44e8bd8446a7c255ef83e) add failing test (Werner M. Krauß)
 * 2020-03-23 [005fdbf](https://github.com/silverstripe/cwp/commit/005fdbfee4ae3c24e702c98d732a7c1eeebfd085) DOCS include note of MFA TOTP secret key as a default env variable (brynwhyman)
 * 2020-03-16 [fb7849f](https://github.com/silverstripe/silverstripe-mfa/commit/fb7849fb752a229a4b2968c04b88651c6fccfe89) Added missing index.md (Ingo Schommer)
 * 2020-03-16 [1c73fd9](https://github.com/silverstripe/silverstripe-userforms/commit/1c73fd90cabfce2281a6565941ebaf02c9e6aa4f) Revert default value (Steve Boyd)
 * 2020-03-13 [0bbc768](https://github.com/silverstripe/cwp-starter-theme/commit/0bbc7686a167e1b76e44758b6f9da4579c1e32c9) Use https for jquery (Steve Boyd)
 * 2020-03-13 [3b4d317](https://github.com/silverstripe/cwp-watea-theme/commit/3b4d3173f4b84dd6c172cc2910b297f2be172b86) Use https for requesting jquery (Steve Boyd)
 * 2020-03-12 [d4729f7](https://github.com/silverstripe/silverstripe-security-extensions/commit/d4729f71b2e221052375953c38d77ce70e1217b7) DOCS fix readme in typo (brynwhyman)
 * 2020-03-06 [527d248](https://github.com/silverstripe/cwp/commit/527d248e1e68ef2094f878e6029eec02587f7138) DOCS release announcement in change log (brynwhyman)
 * 2020-03-04 [083758e](https://github.com/tractorcow-farm/silverstripe-fluent/commit/083758e550f45b66883b5714e42697ad11591603) After test fixes (Nemanja Karadzic)
 * 2020-03-04 [85cb99f](https://github.com/tractorcow-farm/silverstripe-fluent/commit/85cb99f9dd8ff24fbe3481becdcb2468e72ccdf0) Added fix for max version subselect (Nemanja Karadzic)
 * 2020-02-27 [0951f3c](https://github.com/silverstripe/cwp-search/commit/0951f3ce6666e5777cae9a8959868fa8d1f11a3c) Allow environment configuration of indexstore port in local environments (Michal Kleiner)
 * 2020-02-27 [e346926](https://github.com/silverstripe/silverstripe-realme/commit/e346926335e2c16b8d54b47fc682d530256b2655) Readme updates for the 4.0.0 release (Serge Latyntcev)
 * 2020-02-24 [8a3cfc3](https://github.com/silverstripe/silverstripe-realme/commit/8a3cfc317aa522a424b5f831be47d9084ed3e3bf) RealMeService::retrieveFederatedLogonTag implementation (Serge Latyntcev)
 * 2020-02-12 [c41522b](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/c41522be721df4fdd1110664e608eb3d2f8da47e) Use 2.x-dev requirements (Steve Boyd)
 * 2020-02-10 [1e2019b](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/1e2019ba4f69a2863489088a491aad35054aa2c4) Update SearchUpdateQueuedJobProcessor.php (Naomi Guyer)
 * 2020-02-03 [e4be304](https://github.com/silverstripe/cwp/commit/e4be304fa7c0cbdcf61a890f55de533cb2044d37) Update SS3 guide and tweak the SS4 message. (Mateusz Uzdowski)
 * 2020-01-31 [d1b3b16](https://github.com/silverstripe/silverstripe-userforms/commit/d1b3b1629e0aeedd23785c1c1523414382e05887) DOCS fixing anchor syntax (Bryn Whyman)
 * 2020-01-31 [0106683](https://github.com/silverstripe/silverstripe-userforms/commit/0106683d57cf2f283e9b8b4a67581fe4cf3b2d1b) DOCS additional note to keeping uploaded files secure (brynwhyman)
 * 2020-01-29 [59a8902](https://github.com/silverstripe/cwp/commit/59a89027fd1f2fe67ad7e431d63d9b36f854f007) Update external http request documentation (proxy information). (Mateusz Uzdowski)
 * 2020-01-22 [10acb2b](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/10acb2b5409ddf6e3dbbedf285efd117592a8626) Include queuedjob tests (Steve Boyd)
 * 2020-01-21 [83f1c3e](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/83f1c3e45fd09a549974dfe02e3e10ff9eab1bfe) Exclude some queuedjobs tests from running (Steve Boyd)
 * 2020-01-16 [1c1fc75](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/1c1fc75e6535d33ba9620096c02edfb305208455) Increase travis memory to 3G (Steve Boyd)
 * 2020-01-13 [e2c05d5](https://github.com/silverstripe/silverstripe-userforms/commit/e2c05d5a5502bfd1a1712aa9c056c954cabc4590) Minor linting adjustment. (Maxime Rainville)
 * 2020-01-12 [39ee08c](https://github.com/silverstripe/silverstripe-userforms/commit/39ee08cff961d3c57123262e31cd4b01112bd741) Remove bool explicit type definition to please PHP 5.6 (Maxime Rainville)
 * 2020-01-12 [d280c54](https://github.com/silverstripe/silverstripe-userforms/commit/d280c5486084e33209f7503403bc263338a9cfe8) Patch and reorganise some test (Maxime Rainville)
 * 2020-01-12 [8d0a5dd](https://github.com/silverstripe/silverstripe-userforms/commit/8d0a5dd093d96acf27e714dd8e57dea5b2de0be5) Add some missing comments (Maxime Rainville)
 * 2020-01-12 [8d7990a](https://github.com/silverstripe/silverstripe-realme/commit/8d7990a9d66a86323f573922b1c4ba79f4438ee9) DOCS clarify expected format of signing certificate (Dylan Wagstaff)
 * 2020-01-12 [3f555b7](https://github.com/silverstripe/cwp-core/commit/3f555b7319e8c013a7ff0e1f2751cdac870b6f32) Use trusty dist (Steve Boyd)
 * 2020-01-10 [a0cedae](https://github.com/silverstripe/silverstripe-userforms/commit/a0cedaeb384393dd083b7ea5d4288ea1b46dc70b) Move conditionalFieldEnabled to EditableFormField as isDisplayed (Maxime Rainville)
 * 2020-01-10 [b536194](https://github.com/silverstripe/silverstripe-userforms/commit/b53619477c02e005097b6ac49eb4befd1953627f) Revert the return type for UserForm::getRequiredFields (Maxime Rainville)
 * 2020-01-08 [cf91d7f](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/cf91d7f54c53e415b24762c0a6006b23c6c74242) Update client dist bundle (Garion Herman)
 * 2019-12-19 [ca10c48](https://github.com/silverstripe/silverstripe-securityreport/commit/ca10c486a275253889bda755e47d7b112fb5a629) META: Add github action to build docs (Aaron Carlino)
 * 2019-12-19 [03e7000](https://github.com/silverstripe/silverstripe-mfa/commit/03e70007a577ef56c4b2e97bd573437d83585db3) META: Add github action to build docs (Aaron Carlino)
 * 2019-12-19 [3cc110f](https://github.com/silverstripe/silverstripe-taxonomy/commit/3cc110f4fd293729331eca4f9f71b0c0fb70de01) META: Add github action to build docs (Aaron Carlino)
 * 2019-12-19 [5d2a2f0](https://github.com/silverstripe/silverstripe-userforms/commit/5d2a2f03af34fc52910ee4f68762a9813ce26017) META: Add github action to build docs (Aaron Carlino)
 * 2019-12-18 [f8a7567](https://github.com/silverstripe/cwp/commit/f8a7567585b534a93b8b0ee38a7b7fb09c9606d9) Remove docs document that should be independent of the recipe. (Mateusz Uzdowski)
 * 2019-12-17 [b9b2f94](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/b9b2f948861750a446e957bc136a9991223f7157) Bump stringstream from 0.0.5 to 0.0.6 (dependabot[bot])
 * 2019-12-16 [1da21ae](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/1da21aee131d64082e744d2947b6fdc47b960d31) Bump lodash.mergewith from 4.6.0 to 4.6.2 (dependabot[bot])
 * 2019-12-15 [a7d4840](https://github.com/silverstripe/cwp-agencyextensions/commit/a7d4840f9bdf4bf57fc7b4672e76b10d5042d483) add comments to explain setOption behaviour (Jakub Dolba)
 * 2019-12-14 [94e0b65](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/94e0b65ba959db3d14b1cff9494e264d893a565c) Bump handlebars from 4.0.12 to 4.5.3 (dependabot[bot])
 * 2019-12-14 [eb02e67](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/eb02e6715d6a9b06901472708d4eccc3c8c33020) Bump sshpk from 1.13.1 to 1.16.1 (dependabot[bot])
 * 2019-12-13 [ec292b3](https://github.com/silverstripe/silverstripe-login-forms/commit/ec292b367fcc12e0869e2282203dbf8f97385b6d) Bump npm from 6.9.0 to 6.13.4 (dependabot[bot])
 * 2019-12-13 [58fd299](https://github.com/silverstripe/silverstripe-userforms/commit/58fd29996dba11d61c711e14caecced7fbd44670) Bump npm from 6.13.0 to 6.13.4 (dependabot[bot])
 * 2019-12-13 [aaa7f8e](https://github.com/silverstripe/cwp-agencyextensions/commit/aaa7f8eafe72191e2a4bf9b84468496a5957d2bf) Bump npm from 6.12.1 to 6.13.4 (dependabot[bot])
 * 2019-12-11 [ba6d00a](https://github.com/silverstripe/silverstripe-realme/commit/ba6d00a470a40e1995a9d831e7b8976af6a7a4fe) Check for current controller (David Craig)
 * 2019-12-11 [c798006](https://github.com/silverstripe/cwp/commit/c798006aa83746617a5565454a61f36d24fd5110) Add test for DatedUpdateHolderControllerTest (Elliot Sawyer)
 * 2019-12-05 [b4bd6e7](https://github.com/silverstripe/silverstripe-userforms/commit/b4bd6e7968c6fa2a31038dd67f4c2d93b20f583a) DOCS Data protection and privacy note (Ingo Schommer)
 * 2019-12-05 [c195fb2](https://github.com/silverstripe/silverstripe-userforms/commit/c195fb20e6b2394f78cc28de0bf04d4c5a300052) DOCS Removed outdated maintainer info (Ingo Schommer)
 * 2019-11-28 [57d19da](https://github.com/silverstripe/cwp/commit/57d19da234d3ad97666d03d1c337dd39699fbd75) Update PHP infrastructure considerations with the release of PHP 7.4 (Indy Griffiths)
 * 2019-11-28 [6d1397c](https://github.com/silverstripe/cwp-recipe-kitchen-sink/commit/6d1397cd3ae53711c6a6fe351811c9c6613c0b4e) Bump all matrix entries except authenticators to PHP 7.4 (Garion Herman)
 * 2019-11-26 [46b7451](https://github.com/silverstripe/silverstripe-login-forms/commit/46b74513fc0b33f53036b332253d5a6689089b8c) Removed redundent CSS (James Cocker)
 * 2019-11-25 [7e8807c](https://github.com/silverstripe/cwp-search/commit/7e8807cbd4e37c8e82ac9896b9519d622f4ca560) Travis config update (Serge Latyntcev)
 * 2019-11-25 [4366b6f](https://github.com/silverstripe/silverstripe-securityreport/commit/4366b6fc2ce1e204745fdea1344733f7aa097e89) Travis config update (Serge Latyntcev)
 * 2019-11-22 [dd3ed10](https://github.com/silverstripe/silverstripe-login-forms/commit/dd3ed107730bf1caae899e3d32c2dc473919b99d) Resolve SVG IE11 logo scaling issue (James Cocker)
 * 2019-11-22 [42c37b0](https://github.com/silverstripe/silverstripe-login-forms/commit/42c37b013987bbb08f6f6726889c588e31068d9f) Brand logo CSS improvements (James Cocker)
 * 2019-11-21 [b3ac4ea](https://github.com/silverstripe/silverstripe-login-forms/commit/b3ac4eac42e73dd2f5a0ab25534af14d3f61734b) Travis config update (Serge Latyntcev)
 * 2019-11-18 [bbb0246](https://github.com/silverstripe/silverstripe-akismet/commit/bbb02464f2d001095f90206ab004dfb845d1e385) Travis matrix (Serge Latyntcev)
 * 2019-11-17 [e9e40b5](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/e9e40b55a12f8c8e5bef4936c7567d2707c46d55) Bump macaddress from 0.2.8 to 0.2.9 (dependabot[bot])
 * 2019-11-17 [380cebd](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/380cebd0838d72abb8035886af4898dbb249281d) Bump merge from 1.2.0 to 1.2.1 (dependabot[bot])
 * 2019-11-17 [6fe9838](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/6fe983877e55d8cc211eead778acceae83f07d18) Bump jquery from 3.2.1 to 3.4.0 (dependabot[bot])
 * 2019-11-15 [0c4ca29](https://github.com/silverstripe/silverstripe-userforms/commit/0c4ca29cc4313805c128ed9a6cca9689772c7f6c) Bump root version in Travis config (Garion Herman)
 * 2019-11-15 [617b41f](https://github.com/silverstripe/cwp-agencyextensions/commit/617b41f4154106b8bc6bc4665823f5dc4a6237d9) Bump handlebars from 4.0.12 to 4.5.1 (dependabot[bot])
 * 2019-11-08 [a374043](https://github.com/silverstripe/recipe-authoring-tools/commit/a374043585c4a848e22561d080afa9043aa34726) Patch up the composer.json for the major branch 1 (Serge Latyntcev)
 * 2019-11-08 [6d77da4](https://github.com/silverstripe/recipe-collaboration/commit/6d77da4fcc0e8a9d9d67c32e3ca66be7d38513a3) Patch up the composer.json for the major branch 1 (Serge Latyntcev)
 * 2019-11-08 [ab6b2ac](https://github.com/silverstripe/recipe-reporting-tools/commit/ab6b2acb9c0d8976c225f831d9204c57aafce1ec) Patch up the composer.json for the major branch 1 (Serge Latyntcev)
 * 2019-11-08 [cb2cfe5](https://github.com/silverstripe/cwp-recipe-search/commit/cb2cfe55200e88ba2a22c7ab48ca7013946553d9) Patch up the composer.json for the major branch 2 (Serge Latyntcev)
 * 2019-11-08 [3aa320e](https://github.com/silverstripe/recipe-authoring-tools/commit/3aa320e421a4590f129c10a22ba0f66728ce653d) Patch up the composer.json for the major branch 1 (Serge Latyntcev)
 * 2019-11-08 [4467ac3](https://github.com/silverstripe/recipe-content-blocks/commit/4467ac331435de0e531080b21fc67d81ebff0516) Patch up the composer.json for the major branch 2 (Serge Latyntcev)
 * 2019-11-05 [17fd3bc](https://github.com/silverstripe/silverstripe-realme/commit/17fd3bc164adf94c610d9fa7679f5488543f3b03) Return module own phpcs.xml.dist (Serge Latyntcev)
 * 2019-11-03 [8a39ede](https://github.com/tractorcow-farm/silverstripe-fluent/commit/8a39ede89ecaf0746e36266288884d8b66b9687f) Alias 4 as 4.5 (Damian Mooyman)
 * 2019-11-03 [6e1c77a](https://github.com/silverstripe/silverstripe-blog/commit/6e1c77af0bd08a01f9abc60ad96d9640561a61a1) Add missing widget models to db classname remapping (Dylan Wagstaff)
 * 2019-10-23 [80ef172](https://github.com/silverstripe/silverstripe-realme/commit/80ef172924b31d287525d0bfa38c996946395e77) make css more specific as to avoid interference (#48) (Dylan Wagstaff)
 * 2019-10-22 [1a6d997](https://github.com/silverstripe/silverstripe-realme/commit/1a6d997d4b8aaaab73aff1901fbbd5e95b33cc30) DOCS update readme with relevant info for version 4 (#49) (Dylan Wagstaff)
 * 2019-10-21 [d18a077](https://github.com/tractorcow-farm/silverstripe-fluent/commit/d18a077aa3e93db6f25cd7639f07241ddb898488) addFluentBadge should return existing badge (wernerkrauss)
 * 2019-10-16 [239a815](https://github.com/tractorcow-farm/silverstripe-fluent/commit/239a815f8ba625f3a36004b37efe52deda0a1db3) Add docs for adding fluent to an existing project (wernerkrauss)
 * 2019-10-09 [0e4457a](https://github.com/silverstripe/cwp/commit/0e4457a0a474ad094311311d321808d4a13e441d) DOCS Clarify BasicAuth usage (Ingo Schommer)
 * 2019-09-29 [bfc3c2d](https://github.com/tractorcow-farm/silverstripe-fluent/commit/bfc3c2d497869b66684762dcb504d643a7237786) Update links referencing tractorcow to tractorcow-farm to point to relevant repo and particularly, relevant issues page to prevent 404s. (taoceanz)
 * 2019-09-19 [f6bcab2](https://github.com/tractorcow-farm/silverstripe-fluent/commit/f6bcab238eae0baed105e939138f622b81446832) DOCS document delete policy (Damian Mooyman)
 * 2019-09-13 [5105ace](https://github.com/tractorcow-farm/silverstripe-fluent/commit/5105ace230ecdc09e56a591388f27e02c6d14485) Add Fluent search form class (extends SilverStripe CMS SearchForm) with documentation to enable use of SS Core search with Fluent. Code taken from @baukezwaan gist but with a number of amendments such as defining sql query via HEREDOC, keyword patterns definition defined within multidimensional array and processed by foreach, updated to query via ORM with inner join to maintain order by relevance, and added pagination to results. (taoceanz)
 * 2019-09-11 [867118c](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/867118c6f40407716214ca5c7f04bb169cfe4662) Added translatable title (Jani Kiviranta)
 * 2019-09-11 [6fc5a6f](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/6fc5a6fb34c623178461039196d8e13dfb5f1973) Add type mappings for dbdatetime (Will Rossiter)
 * 2019-09-11 [c7fc832](https://github.com/silverstripe/silverstripe-mfa/commit/c7fc8329b1299f11a3ae253ddc19ebc24b5cce5c) DOCS userhelp typo (Bryn Whyman)
 * 2019-09-10 [d273930](https://github.com/silverstripe/silverstripe-mfa/commit/d2739301e7fd3d53a4218ac0f2c76cae5f2c56a1) DOCS userhelp note for subsites (brynwhyman)
 * 2019-09-08 [4f3c282](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/4f3c2828a461a670e82655d406ca915b9ee16a84) Changed translation key for more specific (Jani Kiviranta)
 * 2019-08-26 [b054760](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/b054760129a3ca2128a03e5a88f645a7932f0ac4) Removed test characters (Jani Kiviranta)
 * 2019-08-26 [f35fe8e](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/f35fe8ea24de949400da660684c91e3d76f9aee1) Fixes for translations. (Jani Kiviranta)
 * 2019-08-26 [8de398a](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/8de398ae75a835ead7719de10680b50f77bd0f53) Translation + Finnish (Jani Kiviranta)
 * 2019-08-20 [e21a092](https://github.com/silverstripe/silverstripe-comments/commit/e21a09234a3070d3c869218739c9d45f168ced41) Update translations (Guy Marriott)
 * 2019-08-14 [28c4142](https://github.com/silverstripe/cwp-recipe-cms/commit/28c41429cc22cfb7b61104d3213ee6efbc3cb2bb) Switch to SilverStripe 4.4.x (Robbie Averill)
 * 2019-07-19 [f25ae25](https://github.com/silverstripe/silverstripe-taxonomy/commit/f25ae25b572e31d179477c443a551c585b99bd12) DOCS Add trailing full stops (Robbie Averill)
 * 2019-07-19 [127fa61](https://github.com/silverstripe/silverstripe-taxonomy/commit/127fa61004c9356bcbaa069f04b99aed62a02148) Use our own phpcs ruleset (Robbie Averill)
 * 2019-07-19 [6d2bdb6](https://github.com/silverstripe/silverstripe-taxonomy/commit/6d2bdb6665192da58be7e44ee1c389abaa233a3d) Add PHP 7.2-3 and SilverStripe versions to Travis matrix (Robbie Averill)
 * 2019-07-19 [7e81b0f](https://github.com/silverstripe/silverstripe-mfa/commit/7e81b0f9958a5f47776621874c8b5bd3415c1440) DOCS Fix broken links in docs (Guy Marriott)
 * 2019-07-19 [2f8b2d2](https://github.com/silverstripe/silverstripe-taxonomy/commit/2f8b2d220658baaa54982f7a33d8d5b952d3e593) DOCs update taxonomy userdocs (bwhyman)
 * 2019-07-15 [a3e317a](https://github.com/symbiote/silverstripe-advancedworkflow/commit/a3e317adff3c26d1758e1aa7ed8b01aad9b3f379) Swap Postgres back to MySQL in Travis builds (Robbie Averill)
 * 2019-07-09 [0c80787](https://github.com/silverstripe/silverstripe-realme/commit/0c80787cdcfcda956865dee00841b0e6c06dc4e5) Update phone numbers (Indy Griffiths)
 * 2019-07-07 [aa64211](https://github.com/symbiote/silverstripe-advancedworkflow/commit/aa64211229a30d7b45978601e9d4c289e92d77a8) Update travis (Guy Marriott)
 * 2019-07-07 [da3dec7](https://github.com/symbiote/silverstripe-advancedworkflow/commit/da3dec7d6a6ac2564179725b3b594e0cbfb44352) WorkflowReminderJob missing assigned member emails (Will Rossiter)
 * 2019-07-02 [b3fe6d8](https://github.com/silverstripe/silverstripe-comments/commit/b3fe6d82b47e2a317df69f8b1640918689f7153a) Add legacy YAML for upgrading (Will Rossiter)
 * 2019-05-31 [60b5133](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/60b5133a903030ba2567c3304642227bfcf94912) Make docs available in composer require without preferring source to ensure vendor/bin/fulltextsearch_quickstart script has access to the files it needs and users have docs available available locally. (Thomas Ocean)
 * 2019-05-30 [a95c0a5](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/a95c0a5c53445fc47fa44ca8732905820f6aea74) DOC Suggest prefering source when using the quick set up (Maxime Rainville)
 * 2019-05-16 [617501e](https://github.com/silverstripe/silverstripe-comments/commit/617501efed8644f9726bfba23a13dbc85894fcf3) comments extension filters on Parent Class (Heath Dunlop)
 * 2019-05-15 [914f29c](https://github.com/silverstripe/silverstripe-realme/commit/914f29c39a64b63f804d9cdf27ef28fb6d913b17) DOCS Update reference to sake in configuration documentation (Robbie Averill)
 * 2019-05-08 [d60882c](https://github.com/silverstripe/silverstripe-realme/commit/d60882ca0673fd44e88c29cf9c7589df35bdfaba) Minor - fix for character encodings on private key (#41) (torleif)
 * 2019-04-14 [ab8e316](https://github.com/silverstripe/silverstripe-fulltextsearch/commit/ab8e316f5bea087a451131b5d01d7aeb673e3be8) Added php 7.2 support (Magnus Bengtsson)
 * 2019-04-11 [e757c94](https://github.com/silverstripe/silverstripe-environmentcheck/commit/e757c9477cde784ce77753aa634b30ebef0ba808) ADD ConfirmationMiddleware exceptions for the dev routes (Serge Latyntcev)
 * 2019-04-05 [6fe0b18](https://github.com/silverstripe/silverstripe-environmentcheck/commit/6fe0b1889e60678ed374735f037c4e69a87041bb) Update master alias to 2.2 (Damian Mooyman)
 * 2019-03-28 [f040a12](https://github.com/silverstripe/silverstripe-realme/commit/f040a12e590d88fa7b6cde7a37539cab649f333e) DOCS Fix example for testing authentication (Indy Griffiths)
 * 2018-11-29 [fa5bf1c](https://github.com/silverstripe/silverstripe-realme/commit/fa5bf1c38d1ae115d2d7030f5069e88042876a5b) Updated the assert What's RealMe? pop-up text. (#35) (StephenMakrogianni)
 * 2018-11-27 [d558eb7](https://github.com/silverstripe/silverstripe-elemental-bannerblock/commit/d558eb75511735454a14a3ba06361232cf3d3e04) Add supported module badge to readme (Robbie Averill)
 * 2018-11-27 [5018b3c](https://github.com/silverstripe/silverstripe-realme/commit/5018b3c333a92e295d6fd4c87dbc25ab60c9d415) Update configuration.md (Matt Peel)
 * 2018-11-27 [8f2cc50](https://github.com/silverstripe/silverstripe-realme/commit/8f2cc503bf7e823d67ac5727d29ef495ae9de410) Update documentation to reflect changing certificate requirements (Matt Peel)
 * 2018-06-15 [6ae29eb](https://github.com/silverstripe/silverstripe-taxonomy/commit/6ae29eb7cd4055a4025836b753d754403e5082b1) Add supported module badge to readme (Dylan Wagstaff)
 * 2018-05-08 [25bfc9e](https://github.com/silverstripe/silverstripe-userforms/commit/25bfc9eaf54b7fa538391e6b4892423de962df02) Required fields can have display rules. Hidden required fields are dynamically excluded from server and client side validation. (Jess Champion)
<!--- Changes above this line will be automatically regenerated -->