# axi
This is a library and tool used to interface with an [Axidraw](https://axidraw.com/).
It can be used as a python module, and will eventually be released as a python package.

# Usage
Usage as a `python3.8` module:
```bash
$ python3.8 -m axi
```

Install and use as `python3.8` package:
```bash
$ pwd
../axi/
$ python3.8 setup.py develop
$ chmod +x /bin/axi
$ axi
...
$ axi-repl
> (goto 20 20)
> (square 10 10)
```

# Features
* Safe serial connections and resets
  - Allows for arbitrary disconnects/errors, the plotter will always return home.
* Bounds checking
  - Axidraw will never attempt to go out of physical bounds. This depends on your model.

# Roadmap
- [x] Handle serial interrupts and resets
- [ ] [REPL](#repl) interface
- [ ] Network connectivity/control
- [ ] Easy interrupt / resuming
- [ ] USB interface (e.g. controllers, webcam, Wacom tablet)
- [ ] Replacing `pyaxidraw` itself
    
# REPL
Using `axi-repl` will open a serial connection to your Axidraw and allows you to create, store and edit objects on the fly. The intent of this is to make the Axidraw a more interactive experience, hopefully inspiring some creativity. 

Here is an example of the planned usage and utility:

```clojure
$ axi-repl
=============================================
    axi-repl 0.0.0
[Clang 13.1.6 (clang-1316.0.21.2.5)] on darwin
=============================================
> # Config
> (units 'mm)
> (mode 'relative)

> (shape-mode 'center)
> (shape-mode 'corner)

> (finish-plot-action 'return)   # At the end of a plot, where does head go
> (finish-plot-action 'remain)

> (get-bounds-min)
> (set-bounds-min '(50 50))

> (goto '(50 10))
> (set-home)
> (goto 'home)


> # Shapes, plotting, storing etc
> (plot
    (rect
        (pos '(50 50))
        (width 50)
        (height 20)
        (rotation 45)))
> (plot
    (scale
        (/ 4 5)
        (rect
            (pos1 (get-bounds-min))
            (pos2 (get-bounds-max)))))
> (plot
    (spiral
        (pos nil)        # passing nil as pos assumes relative
        (diameter 20)    # total diameter of planned spiral
        (sides 30)       # amount of sides per circle, 3 being triangle, 360 being circle
        (count 50)))     # rotate in a circle 50 times resulting in a d=20 spiral

> (set
    'enormous-amalgomation-of-examples
    (line
        (pos1 'here)
        (pos2 (add 'here (20 -20))))
    (line
        (pos1 nil)                    # nil pos1 -> rel behavior
        (rotate 90)
        (length 50))
    (dotted-line
        (pos1 'here)
        (rotate (degree (div 'pi 4)))
        # hmmm
        (length 5)                    # 5mm long lines
        (gap (unit 'in (/ 1 2)))))    # 1/2" gap between each "line"
> (plot 'enormous-amalgomation-of-examples)

> (set
    'my-grid-shape
    (rect (pos 'here) (width 50) (height 50))
    (rect (pos 'here) (width -50) (height -50)))
> (set
    'my-grid-action
    (plot 'my-grid-shape)
    (goto (add 'here (50 0)))
    (plot (rotate 45 (scale (/ 1 2) 'my-grid-shape)))
    (plot (rotate 90 (scale 2 'my-grid-shape))))

> (do 'my-grid-action)
```
