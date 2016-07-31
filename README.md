node-red-contrib-sequencer
==========================
## Description
These nodes allow you to record and replay msg-object sequences within node red.
The time between each message is measured, so the replay has the same delays as the original sequence.


## Install via NPM

From inside your node-red directory:
```
npm install node-red-contrib-sequencer
```

## What's inside?
It includes two nodes:

1. ```recorder``` : lets you record sequences of objects, which can be replayed by the player node.

2. ```player``` : the output node lets you replay previously or even manually generated sequences.
The player can replay multiple sequences in parallel.


The recorder produces sequences in the following format, which can be read by the player:
```
{
  "name": "DemoSequence",
  "seq": [
    {
      "data": {
        "topic": "MyTopic",
        "payload": "A"
      },
      "delay": 0
    },
    {
      "data": {
        "topic": "MyTopic",
        "payload": "B"
      },
      "delay": 1250
    },
    {
      "data": {
        "topic": "MyTopic",
        "payload": "C"
      },
      "delay": 795
    }
  ]
}
```
- ```name``` you already guessed it, the name of the sequence to call it to play, pause or stop in ```player```
- ```seq``` the actual sequence, which contains an array of element objects.
 - ```data``` this object will be put into a new msg when being replayed.
 - ```delay``` the time in milliseconds, the player waits before the ```data``` content is being sent.

## Usage

To get started, use these commands, to use the nodes.

### Recorder

Following commands are available:

- ```msg.start``` records a new sequence
- ```msg.stop``` stops current recording and return already recorded sequence

When recording stops, the recorded sequence will be emitted as a single message.

### Player

A player node can run multiple sequences in parallel. These are identified by the sequence name.
Following commands are available:

- ```msg.name``` The name of the sequence being played, paused, stopped or loaded. Sequence objects already contain a name, which can be overwritten with this.
- ```msg.sequence``` load sequences into player (format: JSON or sequence object)
- ```msg.play``` plays the loaded sequence(s). Property value = sequence name(s)
- ```msg.pause``` pauses the currently played sequence / proceed with msg.play
- ```msg.stop``` stops the currently played sequence(s) / start over with msg.play
- ```msg.remove``` removes a sequence from the player
- ```msg.enumerate``` emits an array containing all names of currently loaded sequences

play, pause, stop and remove properties take either a string or an array containing the name(s). If the value is an empty string it is assumed, that all loaded sequences shall be played / paused / stopped or removed.

### Example

```
[{"id":"a923577c.465c58","type":"player","z":"e6a28ad.47ae678","name":"Player node","runOnLoad":false,"x":770,"y":300,"wires":[["ff999048.73bd2"]]},{"id":"c899145c.082fa8","type":"inject","z":"e6a28ad.47ae678","name":"","topic":"","payload":"do","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":740,"wires":[["6764ddd5.8515c4"]]},{"id":"96eda8e.4179f58","type":"inject","z":"e6a28ad.47ae678","name":"Stop","topic":"","payload":"MySequence1","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":220,"wires":[["cc106124.dc5e7"]]},{"id":"2fe32836.cdda58","type":"inject","z":"e6a28ad.47ae678","name":"Play","topic":"","payload":"MySequence1","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":140,"wires":[["62e2f3ee.98923c"]]},{"id":"7ce616bc.34b688","type":"inject","z":"e6a28ad.47ae678","name":"Pause","topic":"","payload":"MySequence1","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":180,"wires":[["7a42c6da.fe0168"]]},{"id":"31b64ef5.573502","type":"inject","z":"e6a28ad.47ae678","name":"Load","topic":"","payload":"{\"name\":\"MySequence1\",\"seq\":[{\"data\":{\"topic\":\"Task one\",\"payload\":50},\"delay\":0},{\"data\":{\"payload\":10,\"anyProperty\":\"can be put in here\"},\"delay\":2800},{\"data\":{\"topic\":\"Task III\",\"payload\":\"on\"},\"delay\":4300},{\"data\":{\"topic\":\"my value\",\"payload\":0},\"delay\":2100}]}","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":100,"wires":[["bee47b4a.b9b798"]]},{"id":"62e2f3ee.98923c","type":"change","z":"e6a28ad.47ae678","name":"payload -> play","rules":[{"t":"move","p":"payload","pt":"msg","to":"play","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":380,"y":240,"wires":[["a923577c.465c58"]]},{"id":"ff999048.73bd2","type":"debug","z":"e6a28ad.47ae678","name":"","active":true,"console":"false","complete":"true","x":950,"y":300,"wires":[]},{"id":"bee47b4a.b9b798","type":"change","z":"e6a28ad.47ae678","name":"payload -> sequence","rules":[{"t":"move","p":"payload","pt":"msg","to":"sequence","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":400,"y":200,"wires":[["a923577c.465c58"]]},{"id":"2b83123d.5dda3e","type":"inject","z":"e6a28ad.47ae678","name":"Start","topic":"","payload":"MyRecord1","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":620,"wires":[["f2a1bace.d82888"]]},{"id":"e674a51e.742528","type":"inject","z":"e6a28ad.47ae678","name":"Stop","topic":"","payload":"MyRecord1","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":660,"wires":[["a6b8a9a4.b16798"]]},{"id":"5b6f5bc0.bf9e14","type":"debug","z":"e6a28ad.47ae678","name":"","active":true,"console":"false","complete":"true","x":890,"y":640,"wires":[]},{"id":"a6b8a9a4.b16798","type":"change","z":"e6a28ad.47ae678","name":"payload -> stop","rules":[{"t":"move","p":"payload","pt":"msg","to":"stop","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":320,"y":660,"wires":[["d21b1841.9d7af8"]]},{"id":"d21b1841.9d7af8","type":"recorder","z":"e6a28ad.47ae678","name":"Recorder node","maxElements":"4","maxDuration":"10","startImmediately":false,"x":720,"y":640,"wires":[["5b6f5bc0.bf9e14","a923577c.465c58"]]},{"id":"c1078529.ee7028","type":"inject","z":"e6a28ad.47ae678","name":"","topic":"","payload":"re","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":780,"wires":[["6764ddd5.8515c4"]]},{"id":"4b76cb4f.7174b4","type":"inject","z":"e6a28ad.47ae678","name":"","topic":"","payload":"mi","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":820,"wires":[["6764ddd5.8515c4"]]},{"id":"cecc77a3.479d78","type":"inject","z":"e6a28ad.47ae678","name":"","topic":"","payload":"fa","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":860,"wires":[["6764ddd5.8515c4"]]},{"id":"dbdd609a.7d29b","type":"inject","z":"e6a28ad.47ae678","name":"","topic":"","payload":"so","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":900,"wires":[["6764ddd5.8515c4"]]},{"id":"f7f547d5.9fbbd8","type":"inject","z":"e6a28ad.47ae678","name":"","topic":"","payload":"la","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":940,"wires":[["6764ddd5.8515c4"]]},{"id":"cb86991c.62ece8","type":"inject","z":"e6a28ad.47ae678","name":"","topic":"","payload":"ti","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":980,"wires":[["6764ddd5.8515c4"]]},{"id":"e78e353c.83bb98","type":"inject","z":"e6a28ad.47ae678","name":"Enumerate","topic":"","payload":"","payloadType":"str","repeat":"","crontab":"","once":false,"x":460,"y":120,"wires":[["d32130f4.391c7"]]},{"id":"3b2a2ce0.c4d5d4","type":"inject","z":"e6a28ad.47ae678","name":"Stop","topic":"","payload":"","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":460,"wires":[["cc106124.dc5e7"]]},{"id":"12444aa9.edbbb5","type":"inject","z":"e6a28ad.47ae678","name":"Play","topic":"","payload":"","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":380,"wires":[["62e2f3ee.98923c"]]},{"id":"42516c6c.bdae94","type":"inject","z":"e6a28ad.47ae678","name":"Pause","topic":"","payload":"","payloadType":"str","repeat":"","crontab":"","once":false,"x":130,"y":420,"wires":[["7a42c6da.fe0168"]]},{"id":"a66608e5.5999f8","type":"inject","z":"e6a28ad.47ae678","name":"Load","topic":"","payload":"{\"name\":\"MySequence2\",\"seq\":[{\"data\":{\"topic\":\"\",\"payload\":\"mi\"},\"delay\":0},{\"data\":{\"topic\":\"\",\"payload\":\"fa\"},\"delay\":1250},{\"data\":{\"topic\":\"\",\"payload\":\"so\"},\"delay\":795},{\"data\":{\"topic\":\"\",\"payload\":\"so\"},\"delay\":998},{\"data\":{\"topic\":\"\",\"payload\":\"re\"},\"delay\":2733},{\"data\":{\"topic\":\"\",\"payload\":\"do\"},\"delay\":713},{\"data\":{\"topic\":\"\",\"payload\":\"re\"},\"delay\":1497}]}","payloadType":"json","repeat":"","crontab":"","once":false,"x":130,"y":300,"wires":[["bee47b4a.b9b798"]]},{"id":"7a42c6da.fe0168","type":"change","z":"e6a28ad.47ae678","name":"payload -> pause","rules":[{"t":"move","p":"payload","pt":"msg","to":"pause","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":390,"y":280,"wires":[["a923577c.465c58"]]},{"id":"cc106124.dc5e7","type":"change","z":"e6a28ad.47ae678","name":"payload -> stop","rules":[{"t":"move","p":"payload","pt":"msg","to":"stop","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":380,"y":320,"wires":[["a923577c.465c58"]]},{"id":"d32130f4.391c7","type":"change","z":"e6a28ad.47ae678","name":"payload -> enumerate","rules":[{"t":"move","p":"payload","pt":"msg","to":"enumerate","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":680,"y":120,"wires":[["a923577c.465c58"]]},{"id":"f0833b23.bd3208","type":"comment","z":"e6a28ad.47ae678","name":"Trigger all sequences","info":"","x":120,"y":340,"wires":[]},{"id":"a64f002f.736e9","type":"comment","z":"e6a28ad.47ae678","name":"Load MySequence2","info":"","x":110,"y":260,"wires":[]},{"id":"6764ddd5.8515c4","type":"change","z":"e6a28ad.47ae678","name":"Filter unwanted properties","rules":[{"t":"delete","p":"topic","pt":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":500,"y":740,"wires":[["d21b1841.9d7af8"]]},{"id":"f2a1bace.d82888","type":"change","z":"e6a28ad.47ae678","name":"payload -> start","rules":[{"t":"move","p":"payload","pt":"msg","to":"start","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":320,"y":620,"wires":[["d21b1841.9d7af8"]]},{"id":"54598abc.0b16c4","type":"comment","z":"e6a28ad.47ae678","name":"Play the keys","info":"","x":90,"y":700,"wires":[]},{"id":"7c35f81e.19cb68","type":"comment","z":"e6a28ad.47ae678","name":"Start / Stop recording","info":"","x":120,"y":560,"wires":[]},{"id":"1876f2c7.f5ef4d","type":"comment","z":"e6a28ad.47ae678","name":"Load newly recorded sequence","info":"","x":870,"y":480,"wires":[]},{"id":"c6eddba9.47dcb8","type":"comment","z":"e6a28ad.47ae678","name":"Load and trigger MySequence1","info":"","x":150,"y":60,"wires":[]},{"id":"8a37bd87.8dac4","type":"inject","z":"e6a28ad.47ae678","name":"Remove","topic":"","payload":"","payloadType":"str","repeat":"","crontab":"","once":false,"x":140,"y":500,"wires":[["adfd9183.f2b96"]]},{"id":"adfd9183.f2b96","type":"change","z":"e6a28ad.47ae678","name":"payload -> remove","rules":[{"t":"move","p":"payload","pt":"msg","to":"remove","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":390,"y":360,"wires":[["a923577c.465c58"]]}]
```
