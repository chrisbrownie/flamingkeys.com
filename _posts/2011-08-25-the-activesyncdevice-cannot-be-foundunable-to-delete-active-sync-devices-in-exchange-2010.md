--- layout: post title: The ActiveSyncDevice Cannot be Found–unable to delete Active Sync Devices in Exchange 2010 date:
2011-08-25 09:29:42.000000000 +10:00 type: post published: true status: publish categories: - Technology tags: - ActiveSync
- AD-DS - Email - Exch - Exchange - Mobile meta: _wp_jd_target: '' _wp_jd_bitly: '' _jd_wp_twitter: '' _jd_post_meta_fixed:
'true' dsq_thread_id: '395337277' _jd_tweet_this: 'yes' _jd_twitter: '' _wp_jd_clig: '' _wp_jd_wp: '' _wp_jd_yourls: '' _wp_jd_url:
'' _edit_last: '1' author: login: chris email: chris@flamingkeys.com display_name: Chris first_name: Chris last_name: Brown
--- This error occurs when inheritable permissions are not enabled on a mailbox. You will see this error when trying to remove
an ActiveSync Device: ![]({{ site.cdnbaseurl }}wp-content/uploads/2011/08/image.png") To fix this, first enable `Advanced
Features` in ADUC: ![]({{ site.cdnbaseurl }}wp-content/uploads/2011/08/image1.png) Then open the user’s properties and browse
to the Security tab. Click `Advanced` and then place a check in the box labeled `Include inheritable permissions from this
object’s parent`. Click OK twice to apply. Wait for AD to replicate to your GCs then retry the action in Exchange. It will
now remove the device successfully.