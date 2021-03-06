# Facebook group harvester

Download all data from a Facebook Group: posts, comments (and subcomments), likes, reactions, users, using the [official facebook-sdk module](https://github.com/mobolic/facebook-sdk/) for Python 2.7. WARNING! Under development, not all features are implemented or to be considered stable.

## Installation

Set a [virtual environment](https://virtualenv.pypa.io/en/stable/): `virtualenv facebookenv && source facebookenv/bin/activate`.
I suggest to use the [virtualenvwrapper utility](https://virtualenvwrapper.readthedocs.io/en/latest/):
`mkvirtualenv facebookenv && workon`.

Install dependencies: `pip install -r requirements.txt`.

## Configuration

Copy `example.cnf` to `your_group_name.cnf`.

Edit `your_group_name.cnf` adding your `access_token` (or `app_id` and `app_secret`) and your `group_id`.
You must create a Facebook App to obtain the first three parameters: https://developers.facebook.com/.
If you don't know your group id, you can find it [in the source of the page](http://stackoverflow.com/a/33094493).

Other optional options are `since_datetime` and `until_datetime` to narrow the time window and filter harvested posts.
You can use your favorite format to write datetime, describing it in `datetime_format` option using the *strftime*
syntax: http://strftime.org/. Please escape `%` char with another `%`, so use double `%%` (sic).

All harvested data will be saved in two files: `file_name`.gexf and `file_name`.json. You can customize `file_name` value,
but the default value is {YYYYmmddHHMM}_{group name}.

## Usage

Run: `python fb_group.py your_group_name.cnf`. Standard output is quite verbose.

Harvested data are organized in a graph structure:

* nodes: users, posts, comments;
* edges:
  * user -|is author of|-> post,
  * user -|is author of|-> comment,
  * user -|reacts to|-> post,
  * user -|reacts to|-> comment
  * comment -|in reply to|-> post,
  * comment -|in reply to|-> comment.

There are three types of nodes and three types of edges. Nodes have additional attributes (ie. name of user, message for posts and comments). Only the -|reacts to|-> edge has an attribute, the [type of reaction](https://developers.facebook.com/docs/graph-api/reference/post/reactions) (LIKE, LOVE, WOW, HAHA, SAD, ANGRY, THANKFUL).

This graph is saved in two formats: [GEXF](https://networkx.github.io/documentation/development/reference/generated/networkx.readwrite.gexf.write_gexf.html#networkx.readwrite.gexf.write_gexf) and [JSON](https://networkx.github.io/documentation/development/reference/generated/networkx.readwrite.json_graph.node_link_data.html#networkx.readwrite.json_graph.node_link_data).

### GEXF

It's a [XML based format](https://gephi.org/gexf/format/) suitable to be imported and managed by [Gephi](https://gephi.org/).

### JSON

It's a simple JSON representation of the network: `{ "nodes": [...], "edges": [...] }`. Single nodes are objects with an id and some additional attributes. Singles edges are objects with a source and a target containing nodes' ids. You can use it directly in javascript visualizations, ie. powered by the [d3js library](http://bl.ocks.org/mbostock/4062045).

## Thanks to

Marco Goldin.
