---
layout: single
categories: workshop
title:  "Workshop 1 - custom-test-title"
excerpt: My custom description
<!-- date:   2019-03-04 11:59:55 +0100 -->
<!-- classes: wide -->
author_profile: true
toc: true
toc_label: My Table of Conntents
toc_sticky: true


header:
  teaser: assets/images/main_projects.jpg
  overlay_image: /assets/images/figure.jpg
  <!-- overlay_filter: 0.5 -->
  <!-- overlay_filter: rgba(255, 0, 0, 0.5) -->
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  actions:
    - label: "Download"
      url: "https://github.com"

---


You’ll find this post in your directory.



Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

{% highlight java linenos %}
@Component
public class ValidateBeneficiaryRequestBuilder implements Processor {

	@Override
	public void process(Exchange exchange) throws Exception {
		InternalRequest<ValidateBeneficiaryRequestBody> internalRequest = exchange.getProperty(INTERNAL_REQUEST, InternalRequest.class);
		ValidateBeneficiaryRequestBody validateBeneficiaryRequest = internalRequest.getData();
		Assert.notEmpty(validateBeneficiaryRequest.getAccounts(), "AccountInformation collection must contain at least one account!");
		AccountInformation account = validateBeneficiaryRequest.getAccounts().get(0);

		AccountIdentifier accountIdentifier = new AccountIdentifier();
		if (StringUtils.isNotEmpty(account.getIBAN())) {
			//SEPA payment
			accountIdentifier.setIban(account.getIBAN());
		} else {
			//FPS payment
			accountIdentifier.setId(account.getBankCode() + account.getAccountCode());
		}

		BeneficiaryValidationRequest aibRequest = new BeneficiaryValidationRequest()
				.beneficiary(new Beneficiary()
						.beneficiaryName(account.getName())
						.beneficiaryAddressLine1(account.getAccountHolderStreetName())
						.beneficiaryAddressLine2(account.getAccountHolderAddressLine1())
						.beneficiaryPostCode(account.getAccountHolderPostCode())
						.toAccount(accountIdentifier));

		exchange.getIn().setBody(aibRequest);


	}
}
{% endhighlight %}

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}



Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
