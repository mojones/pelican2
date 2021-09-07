Title: Inventing new animals with a neural network
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: article
Tags: programming, Python, Machine Learning
Slug: inventing-animals-neural-network-python
Authors: Martin


# Inventing new animals with a neural network

I've just built a new computer to do some deep learning experiments, so I though'd I'd start off by checking that everything is working with a fun project - training a neural network to come up with new names for plants and animals.

If you're not interested in the code, and just want to see some funny names, skip the two next bits and scroll down to where it says "Generating species names".

## Setting up

For this project I'm not actually going to be writing any code to create the neural network - instead I'm going to be using [this repo](https://github.com/sherjilozair/char-rnn-tensorflow) which implements a character based recursive neural network (RNN) using Python and the Tensorflow library. **Character based** means that the network is going to "see" one character at a time, and **recursive** means that it's going to operate on the sequence of characters one at a time. For a much better explanation than I could give, check out [this article](http://karpathy.github.io/2015/05/21/rnn-effectiveness/).

If you want to follow along, clone the repo I linked above then install either the `tensorflow` or `tensorflow-gpu` Python packages, depending on whether you want to train using CPU only or a GPU. For the latter, you'll also need to install the relevant libraries - this took me a few attempts to get working due to version missmatches, but [this set of instructions](https://gist.github.com/ashokpant/5c4e9481615f54af4025ab2085f85869) eventually set things right. When you get to the point where you can fire up a Python shell and run `import tensorflow` without errors then you're probably good to go.

## Creating the training data

When training neural networks, more data is generally considered to be better :-) so we'll go straight to the source for our list of names - [the NCBI taxonomy](https://www.ncbi.nlm.nih.gov/taxonomy). We can download a dump of the taxonomy from [this URL](ftp://ftp.ncbi.nih.gov/pub/taxonomy/taxdump.tar.gz), extract it and end up with the two files we need - *names.dmp* contains the actual names, and *nodes.dmp* tells us what rank each name is. For this project we want to treat names of species (like *short-snouted elephant shrew* or *Elephantulus brachyrhynchus*) separately from names of groups of organisms (like *marsupials* or *Metatheria*).

I won't bore you with the details of these files' structure, but here's a Python program that will read the files and create a new text file for each taxonomic rank (species, genus, order, etc.):

```python
# create a dict to store the rank for each taxid
ranks = {}
for line in open('taxonomy/nodes.dmp'):
    (taxid, _, rank, *_) = line.split("\t|\t")
    ranks[taxid] = rank

# create dicts to store the common and scientific names for each taxix
common_names = {}
scientific_names = {}

for line in open("taxonomy/names.dmp"):
    (taxid, name, _, name_type) = line.split("\t|\t")
    name_type = name_type.rstrip("\t|\n")

    # we are only interested in common names and scientific common_names
    # not things like authorities
    if name_type == 'common name' or name_type == 'genbank common name':
        common_names[taxid] = name
    elif name_type == 'scientific name':
        scientific_names[taxid] = name

# write out a single line for each common names
# repeatedly opening files like this is incredibly inefficient, but we only
# have to run this once so it doesn't really matter
for taxid, common_name in common_names.items():
    with open(ranks[taxid] + '.names', 'a') as output:
        output.write("{} ({})\n".format(common_name, scientific_names[taxid]))
```

## Generating species names

Let's take a look at the species names first. The *species.names* file looks like this:

```no-highlight
Tate's Sulawesian shrew rat (Tateomys rhinogradoides)
triangle keelback (Xenochrophis trianguligerus)
orange-bellied Himalayan squirrel (Dremomys lokriah)
Russell's viper (Daboia russelii)
blackback land crab (Gecarcinus lateralis)
```

One name per line, in the standardish format of common name followed by scientific name in parentheses. Let's train our neural network on this input, then ask it to generate a bunch of similar names. The way that this particular RNN implementation works is that we have to specify how many training epochs we want to run. One epoch is a complete run through the training data, so what we're really specifying here is how many times we want the RNN to "see" each name before it tries to generate some more. We're interested in how well the RNN does at coming up with realistic names after different amounts of training, so we'll use a small Python script which will train for 1,2,4,8,16 and 32 epochs then ask the RNN to generate some new names:

```python
import subprocess

input_dir = "species_names"

for epochs in [2 ** i for i in range(0,7)]:
    # first train for the number of epochs we want...
    subprocess.call(
      "python train.py --data_dir {} --save_dir {}.save --num_epochs {}"
      .format(input_dir,input_dir, epochs), shell=True
      )
    # ...then sample from the model
    subprocess.call(
    "python sample.py --save_dir {}.save -n 10000 >epoch_{}_{}.txt"
    .format(input_dir, input_dir, epochs), shell=True)
```

This is mostly just housekeeping, making sure that we don't overwrite file names and that the RNN looks in the right directory for the input data - it expects there to be a file in the input directory called *input.txt*. Remember that for this example, this is just the species names.

After one epoch of training, the RNN makes up names like this:

```no-highlight
hambward'u tald (Butken peoroides)
Chebin sengtil foad dobarbly (Rhixox boitora)
regbef gloud (Mecuphopis frus)
bored (Maluse pryssilefsatti)
Keriolun breunother pinarg (Otomonthus leidagrus)
```

It's already learned a bunch of features from the training data:

 - each name should be on a separate line
 - the second bit of each name pair is in parentheses
 - the second bit of the name starts with a capital letter
 - the second name is normally two words along


 Notice that I'm saying "the second bit" rathar than "the scientific name" so that we remember that the model doesn't know anything about scientific or common names - it's just learning what it sees in the input file.

 Sometimes it makes mistakes - lines like this also appear in the sample after one epoch:

 ```no-highlight
 lasey floid Reunblaw (uJ
 AnartInee grosbotus)
 flowt gerwerr (Gicrosigotus amalostis)atoym shroyboin (Amoudoncum ostertafitei)
 ```

 Interestingly, the scientific names look a lot more convincing at this stage than the common names. I could readily believe that there really was a species called *Otomonthus leidagrus*.

 Incidentally, the input file for the species names is 1.3MB and has 31,233 names in it. Training for one epoch and sampling takes 20 seconds on a 1080 Ti with all the parameters at default.

Let's see how we do with two epochs of training:

```no-highlight
Hadsear scarbear (Gebre anseiensis)
Arglewon rotter (Hylagnus liphritus)
shark magin (Cobrinius puldensi)
leufand cacollen (Rhhorostalis fumpyccippata)
```

With the additional training, the RNN has pretty much stopped making mistakes with the overall format - I can't find any examples of missing or multiple scientific names. And there are even a few hints that it's starting to pick up ideas about common names. For example, some names start to show up that look like animals that are named after their discoverer:

```no-highlight
Bungle's traty fluck-gadfich (Dinvera biluma)
Magad's alfin flong (Mobyena cangellatus)
Peles's toad (Canina arorosa)
```

Which is a pattern that occurs fairly frequently in the training data. Most of the common names are still pretty rubbish though; I picked out a few favourites from this version of the RNN:

```no-highlight
Arglewon rotter (Hylagnus liphritus)
shark magin (Cobrinius puldensi)
Korte strasse mouse (Lunchop-arterti)
Kraspwern maucter's gaided ructake (Lebacia persanee)
Chiche-ternier voubfish (Canipoconius kyrmorhermane)
```

but most of these make the list simply by virtue of being pronouncable (with some effort).

Let's try four epochs of training:

```no-highlight
elepinelian bat frog (Hypiscalus guannaqii)
Chiglone corpon surcul (Hy1mopsoma dermi)
Imalian hinach styching (Hipoblur joddensis)
blonnded giant sal (Pinistomys squisnama)
Tralorphese flerlegarin (Fea)
whistrest robber brog (Arecularia libyana)
```

The big difference that jumps out here is the inclusion of a lot more obvious animal words in the names - here's a sampling:

```no-highlight
noby thingey frog (Glemanthonias tristiaris)
buedforn fly (Sp. phelyplocalopus)
Hcapeld farchfish (Gyanomys Essertatus)
spineskered rat (Psalachoxaeus gobeas)
cripe gutse mouse (Orcaerhilis mullipepti1)
```

Quite a lot of these names have multiple different types of animal in them:

```no-highlight
Hollow-cobest eel bat (Gephelophnya arvigense)
lack-osfish bat snake (Parrohynus fursticarus)
elepinelian bat frog (Hypiscalus guannaqii)
```

I note that the RNN has a fondness for short animal names at this stage (bat, rat, fly, etc.). Some of the best picks from this version are pretty convincing:

```no-highlight
stark-eyed bungle (Gantterus gailensis)
spotted owfish (Strimus curhicis)
palidiesha fly (Atheles kunaria)
lead-taired readfish (etheonge puricolicota)
cat-ferred marshonfish (Colisas emeysinus)
Shurate's hailfish (Pseydripella purcadaria)
Simas grouse (Pyngomidirna altiftonchus)
```

Bumping the training period up to 8 epochs seems to result in only a vague increase in the frequency of convincing names - some of these definitely sound like real species to me:

```no-highlight
stark-toedfin lizard (Totragobius astrica)
pinyper bat (Pritopterus nentalis)
suckerberry bat (Trildus rau)
sallow frog (pydrurus nelthordi)
rufous mouse (Melanurhesca ovivulax)
Arerie tree frog (Hyllongerana penathenai)
```

Glancing through the whole list, there are a lot of frogs, probably because there are a lot in the training data (frogs are colourful and big enough to see without a microscope, which means that they tend to get noticed and named).

The pattern is the same for the 16- and 32-epoch versions, so let's just skip straight to the 64-epoch version (which is where the score stopped improving) and see what we get. Here's a random chunk from the output:

```no-highlight
cardinal gecko (Osteocleops medevolacus)
grauren huna (Oligosoma dakteli)
devil pocay (Tangarois fuscicola)
Malgashan's threedierd (Phoedyurus borrovi)
blue brown moth (Dendrhina arlengensis)
```

Not bad. Four out of five sound like they could be real animals, although probably not well-known ones :-). Looking through the rest of the output we can pick out some colourful plants and animals:

```no-highlight
brown oak (Turdusia japonica)
red spinyfyatella beetle (Deliocochthys sestralis)
red-backed lovery (Taxispicus cinvae)
white-spotted grout (Macropomaria seydiana)
```

And some with names that are very descriptive:

```no-highlight
Spine slender turtle (Musculus mexoxguty)
spot-vented goby (Apogon yunites)
slender awl robin (Musco godanensis)
```

Even if sometimes the descriptions apply to body parts that the creature is unlikely to posess, as in the case of the `yellow-breasted long-tailed forest frog (Idchodomys perii)`.

A nice pattern that emerges here is geographical identifiers in names:

```no-highlight
Texanut river's Gallican fire-lined anole (Anolis abba)
tropical soldiager (Mogpesus purpurum)
Japanese palopessain (Callonolus princebrus)
Southern straw gult (Rhous aecilopa)
African banded bat (Hippocampus kilegi)
```

We get quite a few names with multiple animal types in them, all of which somehow sound quite dangerous:

```no-highlight
Madago-streak goatfish (Chaetodon semilialis)
bear python (Angioneus cornbyi)
Barbert tanglegored fox frog (Leptodontis apoenii)
```

After all, who would want to battle with an animal that combined the abilities of a python and a bear? Although, the `tanglegored fox frog` sounds like it's permanently injured....

Some of the names are biologically implausible, like the `fruit forestfish (Pinux hasmannicum)`, which I guess has left its marine habitat to graze in the forrest canopy, or the `two-throated gar-eye limepit (Toribolatus exocarbaniculus)`, whose second thoat must presumably serve some important evolutionary function. The most unfortunately named animal in this batch is probably the `Arctanengus shitterfly (Anora cf. asoldii)`.

Here are the rest of my favourites - some of these names are really quite lovely and I can easily imagine them being picked deliberately (`Norten's pookberry` in particular sounds like a delicious fruit):

```no-highlight
dusky lizard (Aetophliparia sagula)
laughing leaf frog (Plectroceps cinena)
chameleon bluewing (Frangolayanum limbata)
golden-nose cocket gomme snake (Choenopterus kouelipunctus)
smouthed plum (Vireopis splendenaneenoi)
yellow robber frog (Pristimantis salvatus)
Alpine's stickral cichlid (Purus pearmandylina)
Cemeran big-bands bat (Polymmus caerulis)
Norten's pookberry (Metalus frascineus)
```

## Generating group names

Having been quite impressed with the performance of the RNN on species names, I'm curious to see how it copes with a much smaller training dataset of group names. These are names for groups of plants and animals (things like jellyfishes, birds, cacti, geckos, etc.) Each name also consists of a common and a scientific name, and the format of the training data is very similar to that of the species names:

```no-highlight
sea wasps (Cubozoa)
mammals (Mammalia)
swordfishes (Xiphiidae)
acorn barnacles (Balanidae)
diving beetles (Hygrobiidae)
nutmeg family (Myristicaceae)
```

but there is much less data - just 3,237 names in the file, which I created by concatenating all of the names for the ranks above species.

Because the training dataset is much smaller, training for a single epoch fails to learn even the basics of the name structure:

```
 iooniee
rrdo iaicvttktiirdsMndr nfeus
o innarsbsolPH(teepa
rcae(ade))igoahra
 fernapfgser( lopousysdacx )
```

After 4 epochs we start to see a few lines that follow the `common name (Scientific name)` pattern, but it's not until 32 epochs that I can pick out some plausible names:


```
Jorn worms (Terpenidae)
bunderfishes (Hacpothlopsto)
ola spider family (Solagaceae)
goonderch toads (Hygpidae)
trockfishes (Brombelus)
```

Even after 512 epochs of training, most of the names are gibberish. Some of the most convincing names include obvious animal names:

```no-highlight
lidic crabs (Melagbaria)
gumal salamanders (Oemiodrosamensis cagulus)
heeb frogs (Isdrocaridae)
croab beetles (Grachthyoratidae)
flowerflies (Cercinae)
braps bees (Bucoloida)
warver family (Pholamiaceae)
gred sharks (Hadrantidae)
```

But my favourites are probably the ones that are pronouncable, but which don't (as far as I know) contain any real animal names. These all sound like they would be good names for types of animals in a sci-fi setting:

```no-highlight
eathids (Rhonomatidae)
spimetters (Equus)
cutlifishes & smoths (Plotiodera)
grass spodwiders (Hypranthrhiliinae)
legacles (Holiotthyla)
```

I can totally imagine a group of space travellers emerging from their ship after landing on a planet and finding themselves surrounded by various species of `spimetters`.

I also like the more melifluous names that the network comes up with, like the `millow mites (Gelus)` and `sea tigers (Nostrinae)`. And I have to confess an affection for the oddly redundant `owl tree trees (Onteogatos)`.

## Conclusion and acknowledgements

That's where we're going to leave this experiment, as my eyes are now glazed over from staring at lists of made up animals and trying to figure out how to pronounce them :-) 

Thanks to [sherjilozair](https://github.com/sherjilozair) on GitHub for making the code available. This post was strongly inspired by the ones I've read on [AI weirdness](http://aiweirdness.com/) so check it out if you liked this. And if this kind of thing sounds interesting to you and you'd like to get started with Python, check out the [Python for Biologist online course](/python-for-biologists-online-course). 

PS. If anyone reading this far down has an artistic side, and wants to come up with illustrations for any of these names, please let me know so I can add links here... I'd love to see what other people think a `white-spotted grout` or a `Spine slender turtle` look like. 
