# Mike's ZMK Mod

## Setup

ZMK is built in a dev container with the zmk config mounted into `/workspace/zmk_config`. Instead of mounting the zmk-config into the zmk dev container as a volume, mount it as a binding:

```
  "mounts": [
    {
        "source": "/Users/mikeveldink/src/miryoku_zmk",
        "target": "/workspaces/zmk_config",
        "type": "bind"
    },
    "type=volume,source=zmk-root-user,target=/root",
    // "type=volume,source=zmk-config,target=/workspaces/zmk-config",
    "type=volume,source=zmk-zephyr,target=${containerWorkspaceFolder}/zephyr",
    "type=volume,source=zmk-zephyr-modules,target=${containerWorkspaceFolder}/modules",
    "type=volume,source=zmk-zephyr-tools,target=${containerWorkspaceFolder}/tools"
  ],

```

With this, the development workflow is as follows:

- Open a local VSCode and modify the zmk-config
- Open the ZMK dev container and build the firmware

## Build

In the ZMK dev container:

```
cd app
for d in left right; do \
  west build -d build/$d -p -b nice_nano_v2 -- \
    -DSHIELD=corne_$d -DZMK_CONFIG=/workspaces/zmk_config/config; \
done
```

## Flash

1. In the zmk-config VSCode:

```
for d in left right; do \
  dcp <zmk dev container name>:/workspaces/zmk/app/build/$d/zephyr/zmk.uf2 \
    ~/src/zmk-$d.uf2;
done
```

With the left and right crkbd side: 
2. Connect the side via USB
3. Put the side in boot mode by pressing reset twice quicky. The side should be mounted as external drive 'NICENANO'
4. Drag zmk-{left|right}.uf2 onto the drive
