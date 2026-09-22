# SkyEar

The agent listens to the microphone on an IP camera you already own, flags
sustained engine-like sounds, and matches each one against live aircraft
positions from ADS-B — correcting for how long the sound took to arrive. Every
aircraft that passes nearby is recorded as heard or not heard, which is what
measures the real detection range of the method.

## Nothing about your camera leaves your network

```
┌─ your network ───────────────────────────────┐
│                                              │
│  camera ──audio──▶ SkyEar agent ──┐          │
│                                   │          │
│  never leaves this box:           │          │
│  password, video, exact position  │          │
│                                   │          │
└───────────────────────────────────┼──────────┘
                                    │ one event per sound
                                    ▼
                                 SkyEar
```

**Sent:** when a sound started and how long it lasted, how loud it was against
the background, its frequency shape, which aircraft matched it, and a sensor
position rounded to about 110 metres.

**Never sent:** your camera's address, username or password; video; audio
recordings unless you opt in; your exact coordinates.

This is architectural rather than a promise. The agent runs inside your network
and pushes events out; nothing reaches in. Nothing on the SkyEar site asks for a
camera address, because the servers could not use one — they cannot route to an
address on your network, and a credential store an operator can decrypt is a
target worth attacking.

Your settings live in the add-on's own `/data`, which the Supervisor keeps
across updates, so pairing survives an upgrade. Full detail:
[skyear.lt/privacy](https://skyear.lt/privacy).

## Setting it up

1. Install the add-on and start it.
2. Open **SkyEar** in the sidebar. The setup page is served by the add-on
   itself, so there is no address to find.
3. Pick your camera make, enter its address and password, and press **Test this
   camera**. It reports whether there is audio on the stream before you commit
   to anything.
4. Set the position. *Use my current location* fills it in, and the ground
   elevation is looked up for you. Accuracy matters here: every distance and
   sound-delay figure is measured from this point.
5. Generate a pairing code at [skyear.lt/devices](https://skyear.lt/devices),
   paste it in, and press **Save and connect**.

Your sensor appears on the map within a minute or two. Step-by-step version,
with what to do when a step does not go as described:
[skyear.lt/join](https://skyear.lt/join).

One code pairs one device, once, within fifteen minutes. Adding a second
device later, or reconnecting one you disconnected, needs a fresh code.

## Cameras

**Reolink, Hikvision, Dahua** — address, username and password. Check RTSP is
enabled on the camera, usually under Network or Advanced.

**Ubiquiti UniFi Protect** works differently. The stream comes from your NVR or
UDM rather than the camera, and there is no username or password: a token in
the URL is the credential. In Protect, open the camera → Settings → Advanced →
enable RTSP, copy the URL, and keep the part after the last slash.

Many cameras have no microphone at all, and the box rarely says. You do not
need to find out in advance — the test tells you.

## Adding a second camera

Both cameras are configured on the same panel, and the second one does not need
a pairing code — the agent tells the server which cameras it has, so a new one
registers itself.

1. Open the SkyEar panel. A **Cameras** bar sits above the form once the first
   camera is saved.
2. Press **+ Add another**, fill in the make, address and password, and press
   **Test this camera**.
3. Set that camera's own position, then **Save**.
4. Restart the add-on so it starts listening to it.

Each camera becomes its own sensor on the map, with its own name and its own
detection record. To remove one, open it in the Cameras bar and press **Remove
this camera**; the password is deleted with it.

One limit worth knowing: all cameras share a single ADS-B feed, centred on the
first camera with a radius of about 28 km. A second camera at the same property
is fine. One much further away needs `adsb.lat`, `adsb.lon` and `adsb.radius_nm`
set by hand in `/data/config.json`, or the aircraft near it will not be in the
data the agent is matching against.

## Teaching it what it heard

The agent keeps a short recording of each sound it flags, on this machine, for
fourteen days. Listening to them and saying what they were is how the detector
improves, and it is the one part no algorithm can do for us.

Open the SkyEar panel and follow **Label what it has heard** under *Teach it*.
Sounds that ADS-B cannot explain come first: an event with an aircraft already
matched to it teaches a classifier little that the transponder did not already
say, while the unexplained ones are the bag a drone would fall into.

Clips never leave the machine. Only the label does, and only if you choose to
send it.

## What this can and cannot do yet

Early, and measured rather than claimed. A single sensor near Vilnius currently
hears a minority of the aircraft that pass it, and wind regularly produces
sounds loud enough to be mistaken for one. The map publishes the detection rate
next to the share of time background noise is present, so you can judge a
result rather than trust it.

The most useful thing a sensor produces today is honest negatives: every
aircraft that passes and is *not* heard is recorded too. That is what
establishes the real range of the method.

SkyEar does not send alerts, and will not until the Fire and Rescue Department
and the military have had a say in how that should work.
