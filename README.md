# JavascriptDNSResolver
Resolve DNS lookups via Javascript

Now before anyone complains, this Javascript has to be put in your HTML pages (or PHP etc.). This seems to be due to Javascript callbacks but if anyone can fix it, feel free to do so. Just don't ask me for help since I've tried and given up.

Here is a resolver to see if the domain name has a MX record published i.e. for when people sign up for newsletters etc., this should cut down on the spam@blah.blah type inputs. I'll get around to putting up a functioning signup type thing but if you're reading this, you probably have one already :-)



So, in your HTML fileput this near the top of the <BODY> section....
  Obviously change 'domain' to a sanitised user input. And do proper stuff inside the 'if' sections. 

  ```javascript
  <SCRIPT>
    
    domain = "gmail.com";
    mxFound = false;
    NonValidTLD = false;
    
    function mxCallback() {

      if (mxFound) {
        
          alert(domain + " has valid MX record(s).");

      }
      else {

          if (NonValidTLD) {
               
              alert(domain + " is not a valid domain name.");

         }
         else {
               
              alert(domain + " does not have a mail server.");

         }

      }
      
  }

</script>
```
And at the bottom of your page insert this.....notice the url ends in type=MX, you could change it to A, TXT etc.
  
```javascript
  <script>

        let mxCheck = new Promise(function(mxResolve, mxReject) {

    url ="https://cloudflare-dns.com/dns-query?name=" + encodeURIComponent(domain) + "&type=MX";
    xmlhttp = new XMLHttpRequest();
    xmlhttp.onreadystatechange = function() {

        if (this.readyState == 4 && this.status == 200) {
                
            myJSON = JSON.parse(this.responseText);

            try {
                
                tmp = myJSON.Answer[0].name;
                mxFound = true;
                mxResolve();
            }
            catch(err){ // no MX records so is it even a real domain name?
            
                try {
                    
                    tmp = myJSON.Authority.name;   // if Authority name appears, you've gone back to the root servers
                    NonValidTLD = true;
                    
                }
                catch(err) {
                    
                }
                mxReject();
                    
            }

        } 
            
    };
                        
    xmlhttp.open("GET", url, false);
    xmlhttp.setRequestHeader("Accept","application/dns-json");
    xmlhttp.send(); 
        
    });

    mxCheck.then(
        function(value) {mxCallback();},
        function(error) {mxCallback();}
    );

    </script>
</body>
```

And that's it.
And it goes without saying there are no warrenties implied or gived, it could break at any time if Cloudflare change stuff etc. etc. Basically you're on your own. Enjoy and feel free to share.

