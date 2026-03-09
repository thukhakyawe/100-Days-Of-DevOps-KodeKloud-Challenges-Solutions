🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

On App Server 3 (stapp03):

Find all .js files inside

/var/www/html/media

Copy them to

/media

Preserve their parent directory structure

Do NOT copy the entire directory

The best command for this is find with cp --parents.

1️⃣ Login to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password

mjolnir123

2️⃣ SSH to App Server 3

```
ssh banner@stapp03
```

Password

BigGr33n

3️⃣ Find and Copy .js Files

Run:

```
sudo find /var/www/html/media -type f -name "*.js" -exec cp --parents {} /media \;
```

Explanation of the command

Part	                Meaning
find	                search files
/var/www/html/media	    search location
-type f	                files only
-name "*.js"	        files ending with .js
cp --parents	        preserve directory structure
{}	                    found file
/media	                destination

4️⃣ Verify the Files

Check copied files:

```
ls -R /media
```

You should see directories similar to:


```
[banner@stapp03 ~]$ ls -R /media
/media:
var

/media/var:
www

/media/var/www:
html

/media/var/www/html:
media

/media/var/www/html/media:
wp-admin  wp-content  wp-includes

/media/var/www/html/media/wp-admin:
js

/media/var/www/html/media/wp-admin/js:
accordion.js                  gallery.min.js                  revisions.min.js
accordion.min.js              image-edit.js                   set-post-thumbnail.js
application-passwords.js      image-edit.min.js               set-post-thumbnail.min.js
application-passwords.min.js  inline-edit-post.js             site-health.js
auth-app.js                   inline-edit-post.min.js         site-health.min.js
auth-app.min.js               inline-edit-tax.js              site-icon.js
code-editor.js                inline-edit-tax.min.js          site-icon.min.js
code-editor.min.js            iris.min.js                     svg-painter.js
color-picker.js               language-chooser.js             svg-painter.min.js
color-picker.min.js           language-chooser.min.js         tags-box.js
comment.js                    link.js                         tags-box.min.js
comment.min.js                link.min.js                     tags-suggest.js
common.js                     media-gallery.js                tags-suggest.min.js
common.min.js                 media-gallery.min.js            tags.js
custom-background.js          media-upload.js                 tags.min.js
custom-background.min.js      media-upload.min.js             theme-plugin-editor.js
custom-header.js              media.js                        theme-plugin-editor.min.js
customize-controls.js         media.min.js                    theme.js
customize-controls.min.js     nav-menu.js                     theme.min.js
customize-nav-menus.js        nav-menu.min.js                 updates.js
customize-nav-menus.min.js    password-strength-meter.js      updates.min.js
customize-widgets.js          password-strength-meter.min.js  user-profile.js
customize-widgets.min.js      password-toggle.js              user-profile.min.js
dashboard.js                  password-toggle.min.js          user-suggest.js
dashboard.min.js              plugin-install.js               user-suggest.min.js
edit-comments.js              plugin-install.min.js           widgets
edit-comments.min.js          post.js                         widgets.js
editor-expand.js              post.min.js                     widgets.min.js
editor-expand.min.js          postbox.js                      word-count.js
editor.js                     postbox.min.js                  word-count.min.js
editor.min.js                 privacy-tools.js                xfn.js
farbtastic.js                 privacy-tools.min.js            xfn.min.js
gallery.js                    revisions.js

/media/var/www/html/media/wp-admin/js/widgets:
custom-html-widgets.js      media-gallery-widget.js      media-video-widget.js      text-widgets.js
custom-html-widgets.min.js  media-gallery-widget.min.js  media-video-widget.min.js  text-widgets.min.js
media-audio-widget.js       media-image-widget.js        media-widgets.js
media-audio-widget.min.js   media-image-widget.min.js    media-widgets.min.js

/media/var/www/html/media/wp-content:
plugins

/media/var/www/html/media/wp-content/plugins:
akismet

/media/var/www/html/media/wp-content/plugins/akismet:
_inc

/media/var/www/html/media/wp-content/plugins/akismet/_inc:
akismet-admin.js  akismet-frontend.js  akismet.js

/media/var/www/html/media/wp-includes:
blocks  js

/media/var/www/html/media/wp-includes/blocks:
file  image  navigation  query  search

/media/var/www/html/media/wp-includes/blocks/file:
view.js  view.min.js

/media/var/www/html/media/wp-includes/blocks/image:
view.js  view.min.js

/media/var/www/html/media/wp-includes/blocks/navigation:
view.js  view.min.js

/media/var/www/html/media/wp-includes/blocks/query:
view.js  view.min.js

/media/var/www/html/media/wp-includes/blocks/search:
view.js  view.min.js

/media/var/www/html/media/wp-includes/js:
admin-bar.js                        imagesloaded.min.js      wp-ajax-response.js
admin-bar.min.js                    imgareaselect            wp-ajax-response.min.js
api-request.js                      jcrop                    wp-api.js
api-request.min.js                  jquery                   wp-api.min.js
autosave.js                         json2.js                 wp-auth-check.js
autosave.min.js                     json2.min.js             wp-auth-check.min.js
backbone.js                         masonry.min.js           wp-backbone.js
backbone.min.js                     mce-view.js              wp-backbone.min.js
clipboard.js                        mce-view.min.js          wp-custom-header.js
clipboard.min.js                    media-audiovideo.js      wp-custom-header.min.js
codemirror                          media-audiovideo.min.js  wp-embed-template.js
colorpicker.js                      media-editor.js          wp-embed-template.min.js
colorpicker.min.js                  media-editor.min.js      wp-embed.js
comment-reply.js                    media-grid.js            wp-embed.min.js
comment-reply.min.js                media-grid.min.js        wp-emoji-loader.js
crop                                media-models.js          wp-emoji-loader.min.js
customize-base.js                   media-models.min.js      wp-emoji-release.min.js
customize-base.min.js               media-views.js           wp-emoji.js
customize-loader.js                 media-views.min.js       wp-emoji.min.js
customize-loader.min.js             mediaelement             wp-list-revisions.js
customize-models.js                 plupload                 wp-list-revisions.min.js
customize-models.min.js             quicktags.js             wp-lists.js
customize-preview-nav-menus.js      quicktags.min.js         wp-lists.min.js
customize-preview-nav-menus.min.js  shortcode.js             wp-pointer.js
customize-preview-widgets.js        shortcode.min.js         wp-pointer.min.js
customize-preview-widgets.min.js    swfobject.js             wp-sanitize.js
customize-preview.js                swfobject.min.js         wp-sanitize.min.js
customize-preview.min.js            swfupload                wp-util.js
customize-selective-refresh.js      thickbox                 wp-util.min.js
customize-selective-refresh.min.js  tinymce                  wpdialog.js
customize-views.js                  tw-sack.js               wpdialog.min.js
customize-views.min.js              tw-sack.min.js           wplink.js
dist                                twemoji.js               wplink.min.js
heartbeat.js                        twemoji.min.js           zxcvbn-async.js
heartbeat.min.js                    underscore.js            zxcvbn-async.min.js
hoverIntent.js                      underscore.min.js        zxcvbn.min.js
hoverIntent.min.js                  utils.js
hoverintent-js.min.js               utils.min.js

/media/var/www/html/media/wp-includes/js/codemirror:
codemirror.min.js  csslint.js  esprima.js  fakejshint.js  htmlhint-kses.js  htmlhint.js  jsonlint.js

/media/var/www/html/media/wp-includes/js/crop:
cropper.js

/media/var/www/html/media/wp-includes/js/dist:
a11y.js                                    deprecated.min.js            patterns.min.js
a11y.min.js                                development                  plugins.js
admin-ui.js                                dom-ready.js                 plugins.min.js
admin-ui.min.js                            dom-ready.min.js             preferences-persistence.js
annotations.js                             dom.js                       preferences-persistence.min.js
annotations.min.js                         dom.min.js                   preferences.js
api-fetch.js                               edit-post.js                 preferences.min.js
api-fetch.min.js                           edit-post.min.js             primitives.js
autop.js                                   edit-site.js                 primitives.min.js
autop.min.js                               edit-site.min.js             priority-queue.js
base-styles.js                             edit-widgets.js              priority-queue.min.js
base-styles.min.js                         edit-widgets.min.js          private-apis.js
blob.js                                    editor.js                    private-apis.min.js
blob.min.js                                editor.min.js                redux-routine.js
block-directory.js                         element.js                   redux-routine.min.js
block-directory.min.js                     element.min.js               reusable-blocks.js
block-editor.js                            escape-html.js               reusable-blocks.min.js
block-editor.min.js                        escape-html.min.js           rich-text.js
block-library.js                           format-library.js            rich-text.min.js
block-library.min.js                       format-library.min.js        router.js
block-serialization-default-parser.js      hooks.js                     router.min.js
block-serialization-default-parser.min.js  hooks.min.js                 script-modules
blocks.js                                  html-entities.js             server-side-render.js
blocks.min.js                              html-entities.min.js         server-side-render.min.js
commands.js                                i18n.js                      shortcode.js
commands.min.js                            i18n.min.js                  shortcode.min.js
components.js                              is-shallow-equal.js          style-engine.js
components.min.js                          is-shallow-equal.min.js      style-engine.min.js
compose.js                                 keyboard-shortcuts.js        token-list.js
compose.min.js                             keyboard-shortcuts.min.js    token-list.min.js
core-commands.js                           keycodes.js                  url.js
core-commands.min.js                       keycodes.min.js              url.min.js
core-data.js                               latex-to-mathml.js           vendor
core-data.min.js                           latex-to-mathml.min.js       viewport.js
customize-widgets.js                       list-reusable-blocks.js      viewport.min.js
customize-widgets.min.js                   list-reusable-blocks.min.js  views.js
data-controls.js                           media-utils.js               views.min.js
data-controls.min.js                       media-utils.min.js           warning.js
data.js                                    notices.js                   warning.min.js
data.min.js                                notices.min.js               widgets.js
date.js                                    nux.js                       widgets.min.js
date.min.js                                nux.min.js                   wordcount.js
deprecated.js                              patterns.js                  wordcount.min.js

/media/var/www/html/media/wp-includes/js/dist/development:
react-refresh-entry.js  react-refresh-entry.min.js  react-refresh-runtime.js  react-refresh-runtime.min.js

/media/var/www/html/media/wp-includes/js/dist/script-modules:
a11y  block-editor  block-library  interactivity  interactivity-router

/media/var/www/html/media/wp-includes/js/dist/script-modules/a11y:
index.js  index.min.js

/media/var/www/html/media/wp-includes/js/dist/script-modules/block-editor:
utils

/media/var/www/html/media/wp-includes/js/dist/script-modules/block-editor/utils:
fit-text-frontend.js  fit-text-frontend.min.js

/media/var/www/html/media/wp-includes/js/dist/script-modules/block-library:
accordion  file  form  image  navigation  query  search

/media/var/www/html/media/wp-includes/js/dist/script-modules/block-library/accordion:
view.js  view.min.js

/media/var/www/html/media/wp-includes/js/dist/script-modules/block-library/file:
view.js  view.min.js

/media/var/www/html/media/wp-includes/js/dist/script-modules/block-library/form:
view.js  view.min.js

/media/var/www/html/media/wp-includes/js/dist/script-modules/block-library/image:
view.js  view.min.js

/media/var/www/html/media/wp-includes/js/dist/script-modules/block-library/navigation:
view.js  view.min.js

/media/var/www/html/media/wp-includes/js/dist/script-modules/block-library/query:
view.js  view.min.js

/media/var/www/html/media/wp-includes/js/dist/script-modules/block-library/search:
view.js  view.min.js

/media/var/www/html/media/wp-includes/js/dist/script-modules/interactivity:
debug.js  debug.min.js  index.js  index.min.js

/media/var/www/html/media/wp-includes/js/dist/script-modules/interactivity-router:
index.js  index.min.js

/media/var/www/html/media/wp-includes/js/dist/vendor:
lodash.js                 regenerator-runtime.js              wp-polyfill-inert.js
lodash.min.js             regenerator-runtime.min.js          wp-polyfill-inert.min.js
moment.js                 wp-polyfill-dom-rect.js             wp-polyfill-node-contains.js
moment.min.js             wp-polyfill-dom-rect.min.js         wp-polyfill-node-contains.min.js
react-dom.js              wp-polyfill-element-closest.js      wp-polyfill-object-fit.js
react-dom.min.js          wp-polyfill-element-closest.min.js  wp-polyfill-object-fit.min.js
react-jsx-runtime.js      wp-polyfill-fetch.js                wp-polyfill-url.js
react-jsx-runtime.min.js  wp-polyfill-fetch.min.js            wp-polyfill-url.min.js
react.js                  wp-polyfill-formdata.js             wp-polyfill.js
react.min.js              wp-polyfill-formdata.min.js         wp-polyfill.min.js

/media/var/www/html/media/wp-includes/js/imgareaselect:
jquery.imgareaselect.js  jquery.imgareaselect.min.js

/media/var/www/html/media/wp-includes/js/jcrop:
jquery.Jcrop.min.js

/media/var/www/html/media/wp-includes/js/jquery:
jquery-migrate.js      jquery.hotkeys.js      jquery.query.js              jquery.ui.touch-punch.js
jquery-migrate.min.js  jquery.hotkeys.min.js  jquery.schedule.js           suggest.js
jquery.color.min.js    jquery.js              jquery.serialize-object.js   suggest.min.js
jquery.form.js         jquery.masonry.min.js  jquery.table-hotkeys.js      ui
jquery.form.min.js     jquery.min.js          jquery.table-hotkeys.min.js

/media/var/www/html/media/wp-includes/js/jquery/ui:
accordion.js          droppable.min.js         effect-pulsate.js       progressbar.min.js
accordion.min.js      effect-blind.js          effect-pulsate.min.js   resizable.js
autocomplete.js       effect-blind.min.js      effect-scale.js         resizable.min.js
autocomplete.min.js   effect-bounce.js         effect-scale.min.js     selectable.js
button.js             effect-bounce.min.js     effect-shake.js         selectable.min.js
button.min.js         effect-clip.js           effect-shake.min.js     selectmenu.js
checkboxradio.js      effect-clip.min.js       effect-size.js          selectmenu.min.js
checkboxradio.min.js  effect-drop.js           effect-size.min.js      slider.js
controlgroup.js       effect-drop.min.js       effect-slide.js         slider.min.js
controlgroup.min.js   effect-explode.js        effect-slide.min.js     sortable.js
core.js               effect-explode.min.js    effect-transfer.js      sortable.min.js
core.min.js           effect-fade.js           effect-transfer.min.js  spinner.js
datepicker.js         effect-fade.min.js       effect.js               spinner.min.js
datepicker.min.js     effect-fold.js           effect.min.js           tabs.js
dialog.js             effect-fold.min.js       menu.js                 tabs.min.js
dialog.min.js         effect-highlight.js      menu.min.js             tooltip.js
draggable.js          effect-highlight.min.js  mouse.js                tooltip.min.js
draggable.min.js      effect-puff.js           mouse.min.js
droppable.js          effect-puff.min.js       progressbar.js

/media/var/www/html/media/wp-includes/js/mediaelement:
mediaelement-and-player.js      mediaelement-migrate.min.js  renderers               wp-playlist.js
mediaelement-and-player.min.js  mediaelement.js              wp-mediaelement.js      wp-playlist.min.js
mediaelement-migrate.js         mediaelement.min.js          wp-mediaelement.min.js

/media/var/www/html/media/wp-includes/js/mediaelement/renderers:
vimeo.js  vimeo.min.js

/media/var/www/html/media/wp-includes/js/plupload:
handlers.js      moxie.js      plupload.js      wp-plupload.js
handlers.min.js  moxie.min.js  plupload.min.js  wp-plupload.min.js

/media/var/www/html/media/wp-includes/js/swfupload:
handlers.js  handlers.min.js  swfupload.js

/media/var/www/html/media/wp-includes/js/thickbox:
thickbox.js

/media/var/www/html/media/wp-includes/js/tinymce:
langs  plugins  themes  tiny_mce_popup.js  tinymce.min.js  utils  wp-tinymce.js

/media/var/www/html/media/wp-includes/js/tinymce/langs:
wp-langs-en.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins:
charmap      directionality  image  media     textcolor     wpdialogs    wpgallery      wpview
colorpicker  fullscreen      link   paste     wordpress     wpeditimage  wplink
compat3x     hr              lists  tabfocus  wpautoresize  wpemoji      wptextpattern

/media/var/www/html/media/wp-includes/js/tinymce/plugins/charmap:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/colorpicker:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/compat3x:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/directionality:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/fullscreen:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/hr:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/image:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/link:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/lists:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/media:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/paste:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/tabfocus:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/textcolor:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/wordpress:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/wpautoresize:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/wpdialogs:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/wpeditimage:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/wpemoji:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/wpgallery:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/wplink:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/wptextpattern:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/plugins/wpview:
plugin.js  plugin.min.js

/media/var/www/html/media/wp-includes/js/tinymce/themes:
inlite  modern

/media/var/www/html/media/wp-includes/js/tinymce/themes/inlite:
theme.js  theme.min.js

/media/var/www/html/media/wp-includes/js/tinymce/themes/modern:
theme.js  theme.min.js

/media/var/www/html/media/wp-includes/js/tinymce/utils:
editable_selects.js  form_utils.js  mctabs.js  validate.js
```

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What the lab is testing

This lab tests three Linux skills:

Skill	                            Command
Searching files	                    find
Filtering by extension	            *.js
Copying with directory structure	cp --parents

These are common DevOps troubleshooting tasks.

Step A — Find .js files

Command part:

find /var/www/html/media -type f -name "*.js"

Meaning:

Option	        Meaning
-type f	        files only
-name "*.js"	JavaScript files

Example files found:

script.js
app.js
main.js

Step B — Copy files with structure

Normally copying files:

cp file.js /media

would place all files in one directory.

But the lab requires preserving the folder structure.

Example:

Original:

/var/www/html/media/js/app/script.js

Destination:

/media/var/www/html/media/js/app/script.js

This is done with:

cp --parents
Why we cannot copy the whole directory

The lab explicitly says:

Do not copy entire /var/www/html/media

So we must copy only .js files, not everything.

---