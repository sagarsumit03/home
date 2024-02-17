# Ingress: 
you can consider ingress as Nginx server which just do the work of forwarding the traffic to services based on the ruleset.

ingress don't have much functionality like API gateway. Some of ingress don't support authentication, rate limiting, application routing, security, merging response & request, and other add-ons/plugin options.

# API gateway:

API gateway can also do the work of simple routing but it's mostly gets used when you need higher flexibility, security and configuration options.

There are lots of parameters to compare when you are choosing the Ingress or API gateway however it's more depends on your usecase.

API gateway like KrakenD, Kong are way better compare to ingress have security integration like Oauth plugin, API key option, it support rate-limiting, API aggregation.
