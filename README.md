<br /><br />

<h1 align="center">@substrate/txwrapper</h1>
<h4 align="center">Helper funtions for offline transaction generation.</h4>

<p align="center">
  <a href="https://www.npmjs.com/package/@substrate/txwrapper">
    <img alt="npm" src="https://img.shields.io/npm/v/@substrate/txwrapper.svg" />
  </a>
  <a href="https://github.com/paritytech/txwrapper/actions">
    <img alt="Github Actions" src="https://github.com/paritytech/txwrapper/workflows/pr/badge.svg" />
  </a>
  <a href="https://opensource.org/licenses/Apache-2.0">
    <img alt="apache-2.0" src="https://img.shields.io/badge/License-Apache%202.0-blue.svg" />
  </a>
  <a href="https://david-dm.org/paritytech/txwrapper">
    <img alt="david-dm" src="https://img.shields.io/david/paritytech/txwrapper.svg" />
  </a>
</p>

<br /><br />

## Note

Please use [`txwrapper-polkadot`](https://github.com/paritytech/txwrapper-core/tree/main/packages/txwrapper-polkadot) instead of this package for long term support and more features. This package will be phased out in the future in favor of the packages in [`txwrapper-core`](https://github.com/paritytech/txwrapper-core/).

If you are a chain builder looking to build a chain specific `txwrapper` please take a look at [`txwrapper-core`](https://github.com/paritytech/txwrapper-core) and the [guide for chain builders](https://github.com/paritytech/txwrapper-core/blob/main/CHAIN_BUILDER.md). 

Any feedback for txwrapper-core and the transition from this package is welcome, just make [an issue at that repo](https://github.com/paritytech/txwrapper-core/issues).

## Get Started

```bash
yarn add @substrate/txwrapper
```

In a JS/TS file:

```typescript
import {
  createSignedTx,
  createSigningPayload,
  methods,
} from '@substrate/txwrapper';

const unsigned = methods.balance.transfer(
  {
    dest: 'FoQJpPyadYccjavVdTWxpxU7rUEaYhfLCPwXgkfD6Zat9QP',
    value: 100,
  },
  {
    // Additional information needed to construct the transaction offline.
  }
);

const signingPayload = createSigningPayload(unsigned, { registry });
// On your offline device, sign the payload.
const signature = myOfflineSigning(signingPayload);

// `tx` is ready to be broadcasted.
const tx = createSignedTx(unsigned, signature, { metadataRpc, registry });
```

Have a look at the [examples](https://github.com/paritytech/txwrapper/tree/master/examples) to see how you can perform the whole lifecycle of a transaction, from generation to signing to broadcast.

Go to [documentation](https://github.com/paritytech/txwrapper/tree/master/docs/globals.md) to see all available functions.

## Contribute

We welcome contributions. Before submitting your PR, make sure to run the following commands:

- `yarn docs`: Will generate docs based on code comments.
- `yarn test`: Make sure all tests pass.
- `yarn lint`: Make sure your code follows our linting rules. You can also run `yarn lint --fix` to automatically fix some of those errors.

### Note for Maintainers

All the commits in this repo follow the [Conventional Commits spec](https://www.conventionalcommits.org/en/v1.0.0/#summary). When merging a PR, make sure 1/ to
use squash merge and 2/ that the title of the PR follows the Conventional Commits spec.

The history of commits will be used to generate the `CHANGELOG`. To do so, run `yarn deploy` on the master
branch. This command will look at all the commits since the latest tag, bump the package version according
to semver rules, and generate a new `CHANGELOG`.

If you don't want to follow semver or need to do a dry run, consult the [`standard-version` CLI usage](https://github.com/conventional-changelog/standard-version#cli-usag)
docs. Flags for `standard-version` can be passed to `yarn deploy`.

`yarn deploy`, which only does local operations and doesn't push anything, will output more or
less the following lines:

```bash
$ yarn deploy
yarn run v1.21.1
$ yarn build && standard-version -r minor
$ rimraf lib/ && tsc
✔ bumping version in package.json from 0.3.2 to 0.4.0
✔ outputting changes to CHANGELOG.md
✔ committing package.json and CHANGELOG.md
✔ tagging release v0.4.0
ℹ Run `git push --follow-tags origin master && npm publish` to publish
```

To publish the new package, run: `git push --follow-tags origin master && npm publish.`
You must have access to the @substrate organization on npm to publish.

### Roadmap

- API revamp to clarify handling of `registry` and `metadataRpc`.

#### Parachain support

- Factor out utility functions and types (such as `decode`, `getRegistry`, `createMethod` etc) into a library for parachain
teams that allows them to release and maintain txwrapper libraries specific to their parachains. This could be called
`@substrate/txwrapper-core`. This lib will allow a parachain team to set up an offline signing lib with unit tests
quickly and painlessly while allowing users access to a consistent `txwrapper` API across parachains.
- While the core utility will be factored out to its own dependency, dispatchables from generic substrate methods
will be published in a package `@substrate/txwrapper-substrate` and Polkadot/Kusama specific dispatchables will be available in
`@substrate/txwrapper-polkadot`. (This could be in a mono repo, but separate packages.) Parachains then create
their own txwrapper lib using the `txwrapper-core` and publish it as `@{parachain-name}/txwrapper`.
