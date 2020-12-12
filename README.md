# Bash commands for warming cloudfront in prep for launching netX at the Art Institute of Chicago
I'm using csvkit to deal with the csv, I'm not using awk because it's painful to use with csv. csvkit is available from homebrew.

# Get a randomized list of ids from 10000 most popular images
`csvcut -c 4 -K 1 top-images.csv |sort -R>idlist-rand.txt`

# Turn on the firehose!
`awk '{printf "https://www.artic.edu/iiif/2/%s/full/{600,843,200},/0/default.jpg\n",$1}' idlist-rand.txt|xargs -n1 -P16 curl -S -s -o /dev/null -f -w "%{http_code} %{url_effective}\n"`

# Get just the public domain images
`csvgrep -c 5 -m 1 top-images.csv|csvcut -K 1 -c 4 > publicdomain.txt`

# Request them to move them into cloudfront
`awk '{printf "https://www.artic.edu/iiif/2/%s/full/1686,/0/default.jpg\n",$1}' publicdomain.txt|xargs -n1 -P16 curl -S -s -o /dev/null -f -w "%{http_code} %{url_effective}\n"`