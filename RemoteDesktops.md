## GPU-Accelerated Remote Desktop Solutions

|Name              | Open Source  | In Broswer | Dedicated App | 60+ FPS | Windows Hosts  | Linux Hosts | Sound | Clipboard |
| :---:            | :---:        | :---:      | :---:         | :---:   | :---:          | :---:       | :---: | :---:     |
|Moonlight/Sunshine| ✅ GPL3.0    | ❌          | ✅            | ✅      |  ✅            | ✅           | ✅    | ❌        |
|RDP/xRDP          | ✅ Apache2   | ❌          | ✅            | ❌      |  ✅            | ✅           | ✅    | ✅        |
|VNC (Tight/Tiger) | ✅ GNU GPL   | ❌          | ✅            | ❌      |  ✅            | ✅           | ❌    | ❌        |
|NoVNC + gStreamer | ✅ MPL 2.0   | ✅          | ❌            | ❌      |  ❌            | ✅           | ✅    | ✅        |
|NiceDCV           | ❌           | ✅          | ✅            | ✅      |  ✅            | ✅           | ✅    | ✅        |
|Parsec            | ❌           | ❌          | ✅            | ✅      |  ✅            | ❌           | ✅    | ✅        |
|Selkies gStreamer | ✅ MPL 2.0   | ✅          | ❌            | ✅      |  ❌            | ✅           | ✅    | ✅        |

## Best for Gaming

Moonlight/Sunshine gets my vote here because of the fantastic latency, image quality, controller support and compatibility with Windows and Linux hosts. It would be my pick for repote desktop too if clipboard support is added. Additionally its free and open-source which makes it very hard to recommend NiceDCV or Parsec. Especially since parsec seems to have a very hard time working around firewalls and NiceDCV's configuration process is hit-and-miss with poor documentation, inconsistant frame-rates, and a reliance on the QUIC protocol for higher performance.

On paper, Selkies gStreamer should be a competitor here but teh fact that its baked into a container with a KDE desktop limits is usefulness and increases the problems with securiing it. For example, installing a game from Steam that uses proton will require special secueity considderations. Additionally I've had too many issues with connectivity, performance, STUN/TURN configurations, and port-forwarding to see it as a viable solution. 

## Best remote Desktop

There are two answers here:

1. If money is no object, NiceDCV provides the most features here. It can be used via the browser as well as via a dedicated app for more perfoemce. When using it on an AWS Ec2 instance it's free, and for all other scenarios there is a 1-month dmeo period that can be extened be re-deployign your VM. Otherwise be prepared to shell out $300 per user.

2. If FOSS is what you want then the best all-around solutions here are xRDP/RDP and NoVNC. RDP is great for basic desktop use, watching videos, coding etc.. but despite workign with GPU-accelration, the low FPS cap on the client makes it difficult to use for gaming. You also will need a dedicated VNC viewer app to use it. NoVNC can provide a similar experince for those who prefer a browser-based tool albeit with a bit worse clipboard experience. Additionally, NoVNC doesnt support sound by default and requires a custom build integratign gStreamer + Websockify.

## Most disappointing

Parsec takes the crown here. Despite a permissive free-tier, technical issues make it hard to want to use as I've spent more time on-call with IT trying to debug why Parsec drops connections, freezes, looses track of input devices etc.. then I've actually gotten to spend on my desktop.
