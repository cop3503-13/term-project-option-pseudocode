# term-project-option-pseudocode
##Project Definition (draft)
At its most basic, a magic mirror is a display of information behind glass.  We propose a system that will configure the type of information to display on the mirror and to compile the information into a useable format.  The system should support multiple configurations and the ability to edit existing configurations.  The system should utilize these configurations to extract data from various sources, transform the data into succinct and relevant information, and output the data.  This output format should be standardized and system agnostic so that it can be used in a variety of situations.

##Project implementation option
Here is an basic option for implementation I put together this evening.  It's a rough draft and just an option.  Feel free to suggest improvements, alternatives, errors, and anything I overlooked.  This is very likely the case, and why we're a group.  If something isn't clear, let me know.  

I tried to make it straightforward but its very possible I left something out or made a typo.  

See files Main, Mirror, Module, ApiModule, WeatherModule, TimeModule









##Note: the below is just some notes on HTTP requests, not the outline. Outline are the files Main, Mirror, Module, ApiModule, TimeModule, WeatherModule


##HTTP requests in c++
Separately I also got http requests working in c++ both with POCO and with Curl/libcurl and http://rapidjson.org/.

After trying both, I actually think POCO is overkill and we should implement Curl/libcurl and rapidjson.  

Here is a very basic rapidjson implementation to read JSON data:
```cpp
#include "rapidjson/document.h"
#include <string>
#include <iostream>

    // 1. Parse a JSON string into DOM.
    const char* json = "{\"project\":\"rapidjson\",\"stars\":"1000"}";
    rapidjson::Document d;
    d.Parse(json);

    std::cout << d["project"].GetString() << " <-- this says rapidjson" << std::end
    std::cout << d["stars"].GetString() << " <-- this says 1000" << std::end
```

And here is a basic curl implementation to get json from an api:
```cpp
#include "curl/curl.h"
#include <string>
#include <iostream>

//this is a callback function to pass to curl
size_t readJsonResponseCallback(char* buffer, size_t size, size_t mem, void* up)
{

    for (int c = 0; c < size*mem; c++)
    {
        responseJSON.push_back(buffer[c]);
    }
    return size*mem;
}

int main()
{
  CURL *curl;
  CURLcode res;

  curl = curl_easy_init();
  if(curl) {
    curl_easy_setopt(curl, CURLOPT_URL, "http://api.openweathermap.org/data/2.5/weather?lat=29.6516&lon=-82.3248&appid=apikeyhere");
    //here we set the option WRITEFUNCTION to our callback function, which is a function we tell CURL to use
    //when it finishes getting the data.
    curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, &readJsonResponseCallback);

    /* Perform the request, res will get the return code */
    res = curl_easy_perform(curl);
    /* Check for errors */
    if(res != CURLE_OK)
      fprintf(stderr, "curl_easy_perform() failed: %s\n",
              curl_easy_strerror(res));

    /* always cleanup */
    curl_easy_cleanup(curl);
    std::cout << "here is the json from curl: " << responseJSON << std::endl;
  }
  return 0;
}
```
