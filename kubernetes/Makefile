.PHONY: update
update:
	docker rm -f $(shell docker ps -a --filter "name=Gitbook-kubernetes" --format "{{.ID}}") || true
	#git pull
	docker run -d -v "${PWD}:/gitbook" -p 4000:4000 --name Gitbook-kubernetes billryan/gitbook gitbook serve
	sleep 2
	docker ps -a --filter "name=Gitbook-kubernetes"
