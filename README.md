# Useful R programming language code snippets


## Obtaining session info only with loaded packages
```
pacman::p_load(sessioninfo,gt)
sessioninfo::session_info(include_base = F)$packages %>% 
  as.data.frame() %>% 
  filter(attached == TRUE) %>% 
  select(package,loadedversion) %>% 
  tibble::remove_rownames() %>% 
  gt()
```
This way, we extract current session info and filter only loaded external packages.
