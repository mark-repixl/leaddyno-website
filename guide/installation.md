---
layout: guide
title: "Affiliate Tracking Software & Online Marketing System: LeadDyno"
nav: guide > installation
---

## Installation Instructions

Installing LeadDyno for your site involves four steps:

1. [Setting up a LeadDyno account](#acct)
2. [Installing Visitor Tracking](#visitor_tracking)
3. [Configuring Lead Tracking](#lead_tracking)
4. [Adding Purchase Tracking](#purchase_tracking)

This guide will help you through each step.

<a name="acct"> </a>
### Account Setup

The first step in installing LeadDyno is to sign up for an account [here](https://app.leaddyno.com/users/sign_up)
then get your public and private keys via [Your Account Page](https://app.leaddyno.com/settings/account).

<a class="docs-anchor" id='visitor_tracking'> </a>
### Visitor Tracking

To get visitor tracking going, add the following code each page on your site:

<pre class="prettyprint">
&lt;script type="text/javascript" src="https://static.leaddyno.com/js">&lt;/script>
&lt;script>
  // If you use multiple sub-domains, set the root domain here
  // LeadDyno.domain = "yourdomain.com";
  LeadDyno.key = "<span class="pub-key-rep">YOUR_PUBLIC_KEY</span>";
  LeadDyno.recordVisit();
&lt;/script>
</pre>

<p class="pub-key-inst">
  Where "YOUR_PUBLIC_KEY" is replaced with the public key from your user page above.
</p>

This will load the LeadDyno library and begin tracking where your visitors are coming from.

<div class="alert alert-info">
  <strong>Note:</strong> This code should be on every page of your site, similar to Google Analytics.
</div>

<a class="docs-anchor" id='lead_tracking'> </a>

### Lead Tracking

The next step is to capture the email address of your visitors.  There are a few different ways to do this:

#### AutoWatch

The easiest way to track leads is to call `autoWatch()`, which will automatically watch all
inputs on your site for a lead email.  To enable this, simply add the following code:

<pre class="prettyprint">
  LeadDyno.autoWatch();
</pre>

#### Watch

If you are familiar with [jQuery](http://jquery.com/), you can use selectors to tell LeadDyno to
watch particular inputs for a lead email:

<pre class="prettyprint">
  LeadDyno.watch("#email"); // will watch fields with the ID 'email'
</pre>

#### Explicit client-side registerLead()

If you want more control over when the lead email is captured, you can call the `registerLead()` function
in your own client site event listeners (e.g. on a form.submit() event handler):

<pre class="prettyprint">
  $('#my-form').submit(function(){
      LeadDyno.registerLead($("#email").val());
    });
</pre>

#### Template-based registerLead()

If you are using a templating system like PHP or ERB in Rails, you can use a mix of client and server side code.
Here is some example PHP:

<pre>
  LeadDyno.recordLead("&lt;?php echo fetchUserDetails($useraccess)["Email"] ?>");
</pre>

A typical installation would place this on the post-signup landing page.

<a class="docs-anchor" id='purchase_tracking'> </a>

### Purchase Tracking

Purchase tracking can be done in one of three ways:

* Using a purchase tracking integration (e.g. [PayPal](/guide/paypal-integration-guide.html) or Stripe)
* Using the LeadDyno [REST API](http://developer.leaddyno.com/rest-api.html)
* Using the LeadDyno javascript library

#### Using Integrations

If you are using a billing system that we integrate with, purchase tracking is very simple:

* [Paypal Integration Guide](/guide/paypal-integration-guide.html)
* [Recurly Integration Guide](/guide/recurly-integration-guide.html)

#### The REST API

If you have a server-side environment and are not using one of the systems we integrate with, the best
option is to use LeadDyno's REST API.

You will do `POST`s and `DELETE`s to `http://api.leaddyno.com/v1/purchases` to create and cancel purchases, respectively.

Here is example code in a variety of languages:

<ul class="nav nav-tabs">
  <li class="active"><a href="#conv-ruby" data-toggle="tab">Ruby</a></li>
  <li><a href="#conv-php" data-toggle="tab">PHP</a></li>
  <li><a href="#conv-perl" data-toggle="tab">Perl</a></li>
  <li><a href="#conv-java" data-toggle="tab">Java</a></li>
</ul>

<div class="tab-content">
  <div class="tab-pane active" id="conv-ruby">
    <pre class="prettyprint">
  # Create a purchase
  ld_uri = URI.parse("https://api.leaddyno.com/v1/purchases")
  ld_http = Net::HTTP.new(ld_uri.host, ld_uri.port)
  ld_http.use_ssl = true
  ld_post = Net::HTTP::Post.new(ld_uri.request_uri)
  ld_post.set_form_data({"key" => "<span class="priv-key-rep">YOUR_PRIVATE_KEY</span>",
                         "email" => current_user.email,
                         "plan" => current_user.plan})
  ld_response = ld_http.request(ld_post)

  # Cancel all purchases for a customer
  ld_uri = URI.parse("https://api.leaddyno.com/v1/purchases")
  ld_http = Net::HTTP.new(ld_uri.host, ld_uri.port)
  ld_http.use_ssl = true
  ld_delete = Net::HTTP::Delete.new(ld_uri.request_uri &plus;
                "?key=<span class="priv-key-rep">YOUR_PRIVATE_KEY</span>" &plus;
                "&amp;email=#{CGI.escape(current_user.email)}")
  ld_response = ld_http.request(ld_delete)
    </pre>
  </div>

  <div class="tab-pane" id="conv-php">
    <pre class="prettyprint">
 /* Create a purchase */
 $url = 'https://api.leaddyno.com/v1/purchases';
 $req = array('key' => '<span class="priv-key-rep">YOUR_PRIVATE_KEY</span>',
              'email' => $account->contact_email,
              'plan' => $plan);
 $fields_string =http_build_query($req);
 $ch = curl_init();
 curl_setopt($ch,CURLOPT_URL,$url);
 curl_setopt($ch,CURLOPT_POST,1);
 curl_setopt($ch,CURLOPT_POSTFIELDS,$fields_string);
 curl_setopt($ch,CURLOPT_RETURNTRANSFER,1);
 $afResult = curl_exec($ch);
 curl_close($ch);
 $afJson = json_decode($afResult);

 /* Cancel all purchases for a customer */
    </pre>
  </div>

  <div class="tab-pane" id="conv-perl">
    <pre class="prettyprint">
  # Create a purchase
  use LWP::UserAgent;
  my $ld_url = 'https://api.leaddyno.com/v1/purchases';
  my $postdata = {"key" => "<span class="priv-key-rep">YOUR_PRIVATE_KEY</span>",
                  "email" => $user_email,
                  "plan" => $user_plan};
  my $ua = LWP::UserAgent->new;
  my $response = $ua->post($ld_url, $postdata);
  if ($response->is_success) {
    print $response->content;
  } else {
    print "Post Failed!\n";
  }

  # Cancel all purchases for a customer
    </pre>
  </div>

  <div class="tab-pane" id="conv-java">
    <pre class="prettyprint">
  // Create a purchase
  HttpGet method = new HttpGet("https://api.leaddyno.com/v1/purchases");
  method.setParams(new BasicHttpParams()
    .setParameter("key", "<span class="priv-key-rep">YOUR_PRIVATE_KEY</span>")
    .setParameter("email", userEmail));
  HttpClient client = new DefaultHttpClient();
  try {
    HttpResponse httpResponse = client.execute(method);
    System.out.println("Response code: " +
                        httpResponse.getStatusLine().getStatusCode());
  } catch (IOException e) {
    e.printStackTrace();
  }

  // Cancel all purchases for a customer
  HttpDelete method = new HttpDelete("https://api.leaddyno.com/v1/purchases");
  method.setParams(new BasicHttpParams()
    .setParameter("key", "<span class="priv-key-rep">YOUR_PRIVATE_KEY</span>")
    .setParameter("email", userEmail));
  HttpClient client = new DefaultHttpClient();
  try {
    HttpResponse httpResponse = client.execute(method);
    System.out.println("Response code: " +
                        httpResponse.getStatusLine().getStatusCode());
  } catch (IOException e) {
    e.printStackTrace();
  }
    </pre>
  </div>
</div>

Additional parameters and documentation can be found in the [Purchases](http://developer.leaddyno.com/rest-api.html#purchases) API
docs.

#### Using Javascript

To track purchases via the Javascript library, add the following javascript code to the 'Thank You' page
*after* a user has purchased:

<pre>
  LeadDyno.recordPurchase();
</pre>

and, to record cancellations, invoke the following javascript:

<pre>
  LeadDyno.recordCancellation();
</pre>

<div class="alert alert-info">
  <strong>Note:</strong> Purchases created and cancelled via the javascript library will need to be approved
  via the <a href="https://app.leaddyno.com/purchases/approve">Pending Purchases</a> and
  <a href="https://app.leaddyno.com/purchases/approve_cancellations">Pending Cancellations</a>
  UIs before they will be completed, in order to prevent fraud.
</div>

