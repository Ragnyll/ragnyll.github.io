---
layout: post
title:  "The Worst Code I've Written"
excerpt: "A quick look at my conky rings and what makes them the worst code I've written (recently)"
date: 2020-01-25 02:15:20 -0500
---

&nbsp;&nbsp;&nbsp;&nbsp;I've been a software engineer for just about 3 years now, and I've gotten pretty confident in my coding ability.
 In fact, I've gotten a little to confident.

&nbsp;&nbsp;&nbsp;&nbsp;I recently wrote a big conky script for drawing some system monitors in bspwm, and if I have to say it doesnt look too bad.

 ![background with conky](/assets/2020-01-25-the-worst-code-ive-written/background_with_conky.png)

&nbsp;&nbsp;&nbsp;&nbsp;I'm a big fan of simple, so I keep my desktop pretty blank aside from the monitors wrapping the side of the screen where I can keep them in my peripheral.
 The bright color is useful for things I'm more interested in keeping an eye on and the subtext is grayed out so if I'm in a translucent terminal I wont see it.
 If my cpu spikes, or my network interface usage spikes I can flip shrink my terminal or go to an empty space to see whats spiking in top process or net usage.
 I've still got space on the top right that I'm planning on putting one more dial in; I just haven't decided what information I want populating that space.

&nbsp;&nbsp;&nbsp;&nbsp;So I've got a decent looking setup.
 It works, I haven't noticed any memory leaking, so where's the skeleton?
 Let's just pop open the code and take a look (here's the scripts in my dotfiles with the commit at the time of writing: [right_side_gaugues.lua](https://gitlab.com/Ragnyll/archdots/blob/034cbaf5ab625d9fbe8134e887a3489578e21961/conky_scripts/right_side_gaugues.lua) and [conky.conf](https://gitlab.com/Ragnyll/archdots/blob/034cbaf5ab625d9fbe8134e887a3489578e21961/config/conky/conky.conf)).

&nbsp;&nbsp;&nbsp;&nbsp;If we look at this from a top down perspective theres a few things going on.
 We're drawing a number of concentric circles, with connectors to the edge of the pane (in the case the side of my screen), an underlined annotation, and some descriptive text beneath it.

 So first lets hit the [code](https://gitlab.com/Ragnyll/archdots/blob/034cbaf5ab625d9fbe8134e887a3489578e21961/conky_scripts/right_side_gaugues.lua#L530] for the rings:
 ```
function ring_gaugue(cr, gaugue_props)
	-- draws a ring gaugue
	-- cr: cairo created surface
	-- gaugue_props: table containing colors (free and used), a meter table, and gaugue metadata
	-- draw_rule: boolean to tell whether to draw a rule connecting the ring to the oriented edge of the cairo space

	if(gaugue_props.rule ~= nil) then
		draw_rule(cr, gaugue_props)
	end
	draw_free_ring(cr, gaugue_props)
	draw_used_ring(cr, gaugue_props)
end
```
&nbsp;&nbsp;&nbsp;&nbsp;The code takes a set of properties of the wring, then draws 2 rings: 1 colored to show usage, then one to show the unused sector.

 The used_ring is lighter, thus giving the illusion that the ring is filling up when usage goes up.
 The `free_ring` and the `used_ring` code are pretty similar, so lets just look at one, the free_ring.
 ```
function draw_free_ring(cr, ring_props)
	-- draws a dark gray ring
	-- cr: cairo created surface
	-- ring_props: table containing colors (free and used), a meter table, and gaugue metadata

	start_angle, end_angle = 0, (2 * math.pi)
	cairo_set_source_rgba(cr, ring_props.colors.free_color.r, ring_props.colors.free_color.g, ring_props.colors.free_color.b, ring_props.colors.free_color.a)
	cairo_set_line_width(cr, ring_props.meter.w)
	cairo_set_line_cap(cr, CAIRO_LINE_CAP_BUTT)
	cairo_arc(cr, ring_props.meter.x, ring_props.meter.y, ring_props.meter.r, start_angle, end_angle)
	cairo_stroke(cr)
end
```
&nbsp;&nbsp;&nbsp;&nbsp;This is also not bad, but we see the the complexity of the property object starting to sneak in.
 Now lets see what a "good" configuration object looks like.
```
gaugue_props = {
  gaugue_name = "bat0",
  gaugue_value = conky_parse("${battery_percent BAT0}"),
  gaugue_max = 100,
  orientation = "right",
  colors = {
    free_color = colors.dark_grey,
    used_color = colors.light_grey,
    rule_color = colors.lighter_grey
  },
  meter = {
    x = epicenter.x, -- the center x offset relative to conf top left
    y = epicenter.y, -- the center y offset relative to conf top left
    r = 45,  -- the radius of the guage
    w = 4    -- the width of the stroke
  },
  rule = {
    w = 3,
    l = 365
  },
  annotation = {
    gaugue_name = "BAT0",
    unit = "%",
    value_font = value_font,
    default_font = default_font,
    font_size_large = 24,
    font_size_small = 12,
    value_loc = {
      x = 425,
      y = 353,
    },
    desc_loc = {
      x = 488,
      y = 353,
    },
    text_loc = {
      hr_len = 255,
      x = 425,
      y = 356,
    },
    accent_color = colors.lighter_grey,
    muted_color = colors.lighter_grey
  }
}
```
&nbsp;&nbsp;&nbsp;&nbsp;*YIKES!* that config is pretty beefy, for one gaugue.
 It also has the annotation configuration mixed into it.
 It does a lot though, it draws the free and used ring, defines colors and tells where in the pane the ring should be.
 The thing I'll do differently when the eventual rewrite comes around is I'll seperate the rules and the annotation object and only use the colors defined at the beginning of the file without flexing on them in the configuration object.
 The size of this needs to be reduced in order to make it less confusing.

&nbsp;&nbsp;&nbsp;&nbsp;So this is beefy, but is it really that bad?
_Yes._

&nbsp;&nbsp;&nbsp;&nbsp;The issue comes in when the concentric reings dont follow the same rules.
 Multiple rings share a single annotation, and the text_loc which was supposed to point where the extra text beneath the description was supposed doesnt even exist for all of the rings.
 Add to that the two different font sizes for the desc have to be drawn separately, so I either have to calculate where the small text goes, which is why the `value_loc` and the `desc_loc` are separate objects, so I just hard code it instead of trying to calculate where the text will land and have to be drawn.

&nbsp;&nbsp;&nbsp;&nbsp;Theres a lot of decisions like this all around the code, such as in the [up_and_down_speed](https://gitlab.com/Ragnyll/archdots/blob/034cbaf5ab625d9fbe8134e887a3489578e21961/conky_scripts/right_side_gaugues.lua#L314-361) where I end up completely ignoring the structure I so painstakingly set up and start writing out the text.
 If you dig through my code a little bit more you'll find that I continuously have to make exceptions my own rules in order to make assets render the way I expect them to.
 The lines connecting the rings to the edge of the screen are also a hard coded mess.

&nbsp;&nbsp;&nbsp;&nbsp;What started as a noble effort to write a set of conky resources purely in Lua eventually descended into a convoluted mess of subconfigs and exceptions.
 I was hoping to build a framework people could actually use and reference, but this code base is such utter garbage I can't recommend this to anyone.

&nbsp;&nbsp;&nbsp;&nbsp;I'm not a developer that works on the front end, and my code shows it, but at least I'm not a glorified electron developer. I've learned a lot though. I've picked up a little Lua, I've got some nice looking dials on my screen that don't suck away memory like electron or other heavy utilities, and I've got a working proof of concept for these rings that I can come back to and improve.
