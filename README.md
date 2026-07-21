# sulthonzh/docker-remote-deployment-action

Secure GitHub Action for Docker Compose and Docker Swarm deployments via SSH. Includes input validation, automatic cleanup, and private registry support.

Hardened by [Chainguard](https://www.chainguard.dev) from the upstream action at [https://github.com/sulthonzh/docker-remote-deployment-action](https://github.com/sulthonzh/docker-remote-deployment-action).

## Versions

| Version | Tag | Upstream commit |
|---------|-----|-----------------|
| v1.4.10 | [`v1.4.10`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.10) | [`288e77e`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/288e77e3093f4d1e46c122ff10de03f630c1c089) |
| v1.4.11 | [`v1.4.11`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.11) | [`84b7f6c`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/84b7f6cdd983df14313f5c2dcaafa3873014549a) |
| v1.4.12 | [`v1.4.12`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.12) | [`2a87908`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/2a879087815019fb8e87bc49f410622862106cc7) |
| v1.4.13 | [`v1.4.13`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.13) | [`f349f83`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/f349f83493193395f7db68d4553ab1178a46c7d5) |
| v1.4.14 | [`v1.4.14`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.14) | [`dfc3cd4`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/dfc3cd419ea4d13cf49d3037c1fa350919254b86) |
| v1.4.15 | [`v1.4.15`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.15) | [`939b053`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/939b053c2b7d4d855e1e29f55373f865aed22af7) |
| v1.4.17 | [`v1.4.17`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.17) | [`3e87dfd`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/3e87dfd8f01188c9f00a2f067faddd6ee089d637) |
| v1.4.18 | [`v1.4.18`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.18) | [`f78b213`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/f78b2130e0f630d2b913c7d9198e4fa3717acd56) |
| v1.4.19 | [`v1.4.19`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.19) | [`2d27f05`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/2d27f05f964c381732c99b1aca444a056adb3b4a) |
| v1.4.2 | [`v1.4.2`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.2) | [`6341484`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/634148425bd35ebb937ef845b6b0cd182f134c16) |
| v1.4.21 | [`v1.4.21`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.21) | [`9e61a68`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/9e61a6893dc21e34f451b470c11ca9ae34cf8351) |
| v1.4.22 | [`v1.4.22`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.22) | [`bc056dd`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/bc056dd8ea3930c56ee04458d714d84556c9198a) |
| v1.4.3 | [`v1.4.3`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.3) | [`2fb1a2a`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/2fb1a2a2bbae0ed63a21d7e9e2c4cc1024eeaefc) |
| v1.4.31 | [`v1.4.31`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.31) | [`9f801ce`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/9f801ceadb213a9b15db5ca242230234f4c5a71d) |
| v1.4.32 | [`v1.4.32`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.32) | [`de6102f`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/de6102f79ab0750bbeac3ac16a01eef3a5ca41e8) |
| v1.4.36 | [`v1.4.36`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.36) | [`60cd2fc`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/60cd2fcb0f2bccdc6f7a1e0505d89e95245f1269) |
| v1.4.37 | [`v1.4.37`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.37) | [`9ffc07b`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/9ffc07b632a2fa44b126cc5f81ccf1a88dacdea9) |
| v1.4.38 | [`v1.4.38`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.38) | [`ab28237`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/ab28237d75750b12b682ae2cd8dbd69498f579a0) |
| v1.4.39 | [`v1.4.39`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.39) | [`bcf2c64`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/bcf2c64cd55e0cc69870ce9f81533886908ac49c) |
| v1.4.4 | [`v1.4.4`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.4) | [`ef38e72`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/ef38e72ba088e61a7f05ebb2bda3352418ee0120) |
| v1.4.40 | [`v1.4.40`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.40) | [`eae69a2`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/eae69a2827d43e43b045e2ec6bf7554906a3aec5) |
| v1.4.42 | [`v1.4.42`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.42) | [`7994041`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/7994041545196bd83bc3f3f155d6974d08488329) |
| v1.4.43 | [`v1.4.43`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.43) | [`2c585f4`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/2c585f4df7db5dba5a5859757088a5482ed572c8) |
| v1.4.44 | [`v1.4.44`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.44) | [`59cee1d`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/59cee1d9130112276407f8ea16131ec577fc8ce2) |
| v1.4.45 | [`v1.4.45`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.45) | [`2788633`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/2788633e0a94667da24f9cdd2f01c270194ff6e3) |
| v1.4.46 | [`v1.4.46`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.46) | [`2d56dcd`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/2d56dcdd563ecf8dbb3ab0f79e75e9d9cc42396b) |
| v1.4.47 | [`v1.4.47`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.47) | [`46b6fcf`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/46b6fcf304020684c54d2a3be15702db0220a6a8) |
| v1.4.48 | [`v1.4.48`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.48) | [`f4b33f8`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/f4b33f8bdae5cb4073a639f345f81759186be158) |
| v1.4.5 | [`v1.4.5`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.5) | [`aa5d4dc`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/aa5d4dced40ca1691f667b531ab88883a05d60b2) |
| v1.4.50 | [`v1.4.50`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.50) | [`1fc02ec`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/1fc02ec471a024a2d7bef7815152c37e1233e943) |
| v1.4.51 | [`v1.4.51`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.51) | [`e4850e6`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/e4850e60a322be9dfac9a197c32124246f256d43) |
| v1.4.52 | [`v1.4.52`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.52) | [`ff0aa79`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/ff0aa792dc9826b60bb36df626c924563840c45c) |
| v1.4.53 | [`v1.4.53`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.53) | [`3e609ef`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/3e609efc85dd14a7bfbb6cda558a147cf98950f8) |
| v1.4.54 | [`v1.4.54`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.54) | [`9a17b38`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/9a17b38f86ca251a1275902dd3848d186c1d131c) |
| v1.4.55 | [`v1.4.55`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.55) | [`dd88514`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/dd88514a0f5aa2be97c651ba46d75f967c34bb28) |
| v1.4.57 | [`v1.4.57`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.57) | [`da39fd5`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/da39fd5d99cdb69b233c3b75d1d7f0704fb4faa2) |
| v1.4.58 | [`v1.4.58`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.58) | [`6aa25d4`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/6aa25d4e3b43ba0619b37eb1fea4e326ae134038) |
| v1.4.6 | [`v1.4.6`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.6) | [`dd3fd30`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/dd3fd306946f1ef2326aba571236fdbd91dfffcb) |
| v1.4.64 | [`v1.4.64`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.64) | [`e213686`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/e2136861baf421992089e259c1b5aae6b633ff63) |
| v1.4.65 | [`v1.4.65`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.65) | [`43da507`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/43da5070d70f6e9191cbcc836694ae21c045e854) |
| v1.4.7 | [`v1.4.7`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.7) | [`c509ce5`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/c509ce5dd9a91f9106d7e13aae156124c4cae792) |
| v1.4.8 | [`v1.4.8`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.8) | [`0004a3d`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/0004a3d9ec45b82a5924ed2b9e80b9ad00c14ee3) |
| v1.4.9 | [`v1.4.9`](https://github.com/chainguard-actions/sulthonzh-docker-remote-deployment-action/tree/v1.4.9) | [`9721b46`](https://github.com/sulthonzh/docker-remote-deployment-action/commit/9721b467b01a4f4f4eae3cf404ea853f4c8714b8) |

## Privacy

This Action contacts Chainguard's licensing server to verify authorization. Connection metadata (IP address, GitHub repository identifier, timestamp, and any metadata encoded in the auth token) is transmitted to Chainguard, Inc. even if authorization is denied in accordance with our [Privacy Notice](https://www.chainguard.dev/legal/privacy-notice)
