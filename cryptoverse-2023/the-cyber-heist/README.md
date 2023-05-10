# the cyber heist

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/42868c4e-e21e-43e3-ab65-a24d5391c227)

(also, see pcapng file)

## solution

The pcapng file is a Wireshark USB sniffer dump, obviously.

We see that there are four USB devices, each with their own address.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/93061b7f-97a8-4aa5-a42a-c3856e67d34f)

In the device descriptor of each, we can see the manufacturer and product.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/3b3f4824-8df8-4afa-8409-57edb33392c2)

There are four devices:

- `1.1.0` is a high speed USB hub of some sort
- `1.2.0` has an unknown product id but is made by holtek semiconductor, inc
- `1.3.0` is a razer deathadder 2013 (mouse)
- `1.4.0` is unknown all around, but is later revealed to be a drawing tablet

I know that whoever made this challenge definitely plays the game called osu!, where you need a tablet to be good.

We use the [https://the-sz.com/products/usbid/](https://the-sz.com/products/usbid/) to lookup the idVendor of the unknown device and products and such.

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/f31ed711-3fc0-49ff-965a-00b3562d9ea2)

1.4.0 is a drawing tablet

At first I tried collecting mouse data that may spell out a flag with mouse motions, but I just got this instead:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/4f14b5db-3228-4387-9f3f-5f7a0202685a)

For a while, I was stumped, but then I saw this:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/ec43e083-5797-4847-826b-52d54f82b665)

Holtek semiconductor makes various office computer equipment, like keyboards and mice.

So, I pulled up the HID 1.11 spec [here](https://www.usb.org/sites/default/files/hid1_11.pdf)

I found some keyboard HID examples:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/d6d2baea-8070-4acd-aee9-36b466e69671)

and then did a google search for HID keycodes and found this list [here](https://gist.github.com/MightyPork/6da26e382a7ad91b5496ee55fdc73db2)
I then decoded the flag using the HID data.

One helpful thing was finding [this github thing](https://gist.github.com/mwwhited/263f3f871e05c934884926773d79596f) on HID decoding tips

I used the `usb.addr matches "^1.2"` filter

I also added the HID data column

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/d608dc75-4d83-4be9-b01e-e56c9d69a934)

Then, it was pretty easy to decode by hand
