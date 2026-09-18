# SkyEar

## What this does

The agent listens to the microphone on an IP camera you already own, flags
sustained engine-like sounds, and matches each one against live aircraft
positions from ADS-B — correcting for how long the sound took to arrive. Every
aircraft that passes nearby is recorded as heard or not heard, which is what
measures the real detection range of the method.

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
5. Generate a pairing code at [skyear.vercel.app](https://skyear.vercel.app),
   paste it in, and press **Save and connect**.

Your sensor appears on the map within a minute or two.

## Cameras

**Reolink, Hikvision, Dahua** — address, username and password. Check RTSP is
enabled on the camera, usually under Network or Advanced.

**Ubiquiti UniFi Protect** works differently. The stream comes from your NVR or
UDM rather than the camera, and there is no username or password: a token in
the URL is the credential. In Protect, open the camera → Settings → Advanced →
enable RTSP, copy the URL, and keep the part after the last slash.

Many cameras have no microphone at all, and the box rarely says. You do not
need to find out in advance — the test tells you.

## What leaves your network

**Sent:** when a sound started and how long it lasted, how loud it was against
the background, its frequency shape, which aircraft matched it, and a sensor
position rounded to about 110 metres.

**Never sent:** your camera's address, username or password; video; audio
recordings unless you opt in; your exact coordinates.

This is architectural rather than a promise. The agent runs inside your network
and pushes events out; nothing reaches in. Nothing on the SkyEar site asks for a
camera address, because the servers could not use one.

Configuration lives in the add-on's own `/data`, which the Supervisor keeps
across updates, so pairing survives an upgrade.

## Where this actually stands

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
