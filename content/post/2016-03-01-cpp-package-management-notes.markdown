---
categories: dev
date: 2016-03-01T13:30:00Z
tags: programming C++
title: C++ Package Management notes
url: /2016/03/01/cpp-package-management-notes/
---

How should a package system for C++ work?

Here's one ideal

{{< highlight cpp >}}
#include "curl" // this is a package
#include <iostream> // this is a system header

std::string CurlGet(const char* url);

void main(int argc, char* argv[])
{
	std::cout << "Download file" << std::endl;

	// Download argv[1] into a std::string
	std::string body = CurlGet(argv[1]);
	std::cout << body;
}
{{< / highlight >}}

This is a real program, except for one small detail.

{{< highlight cpp >}}
#include "curl"
{{< / highlight >}}

In an ideal world, this is all you would need to use Curl in your program; no
downloading installing a Curl distribution, no adding of search path and linker
entries to a makefile or other build system artifact.

The function ```CurlGet``` isn't from the curl package, it's my code. I separated it
out just to make it clearer. Here it is, for completeness' sake. Put this together
with the code above and a compiler that supports packages, and you get this:

{{< highlight cpp >}}
size_t WriteStdString(void* contents, size_t size, size_t nmemb, void* userp)
{
	size_t bytes = size * nmemb;
	std::string* str = (std::string*) userp;

	size_t offset = str->length();
	str->append(bytes, ' ');

	// Add this new chunk to the string. Yes, ugly, writing into a string buffer.
	memcpy((char *)str->c_str() + offset, contents, bytes);
	return bytes;
}

std::string CurlGet(const char* url)
{
	std::string body;
	body.reserve(1*1024*1024); // reserve enough for a moderate sized file

	curl_global_init(CURL_GLOBAL_DEFAULT);
	auto session = curl_easy_init();

	curl_easy_setopt(s.session, CURLOPT_URL, argv[1]);
	curl_easy_setopt(s.session, CURLOPT_WRITEFUNCTION, WriteStdString);
	curl_easy_setopt(s.session, CURLOPT_WRITEDATA, (void *)&body);
	curl_easy_setopt(s.session, CURLOPT_USERAGENT, "packagedemo/1.0");

	auto result = curl_easy_perform(s.session);
	/* should really check result, eh? */

	curl_easy_cleanup(session);
	curl_global_cleanup();
}

{{< / highlight >}}

In our current world, if you have a computer without Curl on it, and are not
running an operating system where Curl has been packaged up for you to grab,
you're in for an hour to a day of finding Curl source, building it, and then
adding it to your program.

So how can we get closer to this ideal world?
