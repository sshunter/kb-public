# Mazanke Image Optimizer

" A self-hosted local image optimizer that runs in your browser."

[Github Repo](https://github.com/civilblur/mazanoke)

[DB Tech's Video](https://www.youtube.com/watch?v=aQq0mo5RN34)

**Note:** I haven't tried this yet, but if I get back photography I will want to
check it out.

## Docker

```yaml
services:
  mazanoke:
    container_name: mazanoke
    image: ghcr.io/civilblur/mazanoke:latest
    ports:
      - "3474:80"
    environment:
      - USERNAME=YourUsername
      - PASSWORD=YourPassword      
```

## Other ways to use

This runs in the browser, so you don't even need to run with docker (or even
install). See <https://github.com/civilblur/mazanoke?tab=readme-ov-file#install>
for more information.


## Notes

- Since it does run in browser, I don't have to worry about overloading my poor,
  tiny "server"
