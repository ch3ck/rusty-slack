# rusty-slack
[![Build, Test, and Styling](https://github.com/ch3ck/rusty-slack/actions/workflows/basic.yml/badge.svg)](https://github.com/ch3ck/rusty-slack/actions/workflows/basic.yml)

[![Release new packages](https://github.com/ch3ck/rusty-slack/actions/workflows/release.yml/badge.svg)](https://github.com/ch3ck/rusty-slack/actions/workflows/release.yml)

[![supply-chain security - Cargo audit](https://github.com/ch3ck/rusty-slack/actions/workflows/audit.yml/badge.svg)](https://github.com/ch3ck/rusty-slack/actions/workflows/audit.yml)

[![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE-MIT)
[![Apache licensed](https://img.shields.io/badge/license-Apache-blue.svg)](./LICENSE-APACHE)

A rust crate for sending messages to Slack via webhooks.

This is a maintained fork of [rust-slack](https://github.com/frostly/rust-slack)


[Slack](https://slack.com/) is a messaging platform for team collaboration.

Upgrading? See the [CHANGELOG](./CHANGELOG.md).

Requires rust 1.61 or newer.

# Usage

Add the following to your `Cargo.toml`:

```toml
[dependencies]
slack-hooked = "0.10"
```

or for use with Tokio or Actix:

```toml
[dependencies]
slack-hooked = { version = "0.10", features = ["async"] }
```

Add the crate to your existing project:

```rust,no_run
extern crate slack_hook;
use slack_hook::{Slack, PayloadBuilder};

fn main() {
    let slack = Slack::new("https://hooks.slack.com/services/abc/123/45z").unwrap();
    let p = PayloadBuilder::new()
      .text("test message")
      .channel("#testing")
      .username("My Bot")
      .icon_emoji(":chart_with_upwards_trend:")
      .build()
      .unwrap();

    let res = slack.send(&p);
    match res {
        Ok(()) => println!("ok"),
        Err(x) => println!("ERR: {:?}",x)
    }
}
```

## Attachments

To create a payload with just an attachment:

```rust
extern crate slack_hook;
use slack_hook::{PayloadBuilder, AttachmentBuilder};

fn main() {
  let _ = PayloadBuilder::new()
    .attachments(vec![AttachmentBuilder::new("my text").color("#b13d41").build().unwrap()])
    .build()
    .unwrap();
}
```

## Text with Links

Slack messaging API permits you to send links within text. However, given the different formatting
rules, these text fragments need to be specified as follows:

```rust
extern crate slack_hook;
use slack_hook::{PayloadBuilder, SlackTextContent, SlackLink};
use slack_hook::SlackTextContent::{Text, Link};

fn main() {
  let _ = PayloadBuilder::new()
    .text(vec![
      Text("Hello".into()),
      Link(SlackLink::new("https://google.com", "Google")),
      Text(", nice to know you.".into())
    ].as_slice())
    .build()
    .unwrap();
}
```

Sending this payload will print the following in slack (note: each element of the `Vec` has been
space-separated):

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Hello [Google](https://google.com), nice to know you.

This technique can be used for any function that has the `Into<SlackText>` trait bound.

# License

This library is distributed under similar terms to Rust: dual licensed under the MIT license and the Apache license (version 2.0).

See [LICENSE-APACHE](LICENSE-APACHE), [LICENSE-MIT](LICENSE-MIT), and [COPYRIGHT](COPYRIGHT) for details.
