REPO_DIR=$(shell pwd)

run: download transform format


###############################################################################
# Init - prepare the environment
###############################################################################

init: 
	cd $(REPO_DIR)/parser && npm install


###############################################################################
# Download all the specs
###############################################################################
download: download.api.v1 download.storage.v1 download.tsdoc.v1 download.tsdoc.v2

download.api.v1:
	curl -sS https://api.supabase.com/api/v1-json > $(REPO_DIR)/api_v0_openapi.json
	
download.storage.v1:
	curl -sS https://supabase.github.io/storage-api/api.json > $(REPO_DIR)/storage_v0_openapi.json

download.tsdoc.v1:
	curl -sS https://supabase.github.io/supabase-js/v1/spec.json > $(REPO_DIR)/enrichments/tsdoc_v1/supabase.json
	curl -sS https://supabase.github.io/gotrue-js/v1/spec.json > $(REPO_DIR)/enrichments/tsdoc_v1/gotrue.json
	curl -sS https://supabase.github.io/postgrest-js/v1/spec.json > $(REPO_DIR)/enrichments/tsdoc_v1/postgrest.json
	curl -sS https://supabase.github.io/realtime-js/v1/spec.json > $(REPO_DIR)/enrichments/tsdoc_v1/realtime.json
	curl -sS https://supabase.github.io/storage-js/v1/spec.json > $(REPO_DIR)/enrichments/tsdoc_v1/storage.json
	curl -sS https://supabase.github.io/functions-js/v1/spec.json > $(REPO_DIR)/enrichments/tsdoc_v1/functions.json


download.tsdoc.v2:
	@echo ""
	@echo "WARNING ............................................................"
	@echo "WARNING: tsdoc v2 is not yet available for download"
	@echo "WARNING: files should be updated manually"
	@echo "WARNING ............................................................"
	@echo ""

###############################################################################
# Transform docs into working files
###############################################################################
transform: dereference.api.v0 dereference.storage.v0 dereference.tsdoc.v2 combine.tsdoc.v1 combine.tsdoc.v2

dereference.api.v0:
	npx swagger-cli bundle --dereference --outfile $(REPO_DIR)/transforms/api_v0_openapi_deparsed.json $(REPO_DIR)/api_v0_openapi.json

dereference.storage.v0:
	npx swagger-cli bundle --dereference --outfile $(REPO_DIR)/transforms/storage_v0_openapi_deparsed.json $(REPO_DIR)/storage_v0_openapi.json

dereference.tsdoc.v2:
	cd $(REPO_DIR)/parser && npm run tsdoc:dereference:functions
	cd $(REPO_DIR)/parser && npm run tsdoc:dereference:gotrue
	cd $(REPO_DIR)/parser && npm run tsdoc:dereference:postgrest
	cd $(REPO_DIR)/parser && npm run tsdoc:dereference:realtime
	cd $(REPO_DIR)/parser && npm run tsdoc:dereference:storage
	cd $(REPO_DIR)/parser && npm run tsdoc:dereference:supabase

combine.tsdoc.v1:
	jq -s '{ name: "Combined Specs", children: [.[0], .[1], .[2], .[3], .[4], .[5]] }' \
	$(REPO_DIR)/enrichments/tsdoc_v1/supabase.json \
	$(REPO_DIR)/enrichments/tsdoc_v1/gotrue.json \
	$(REPO_DIR)/enrichments/tsdoc_v1/postgrest.json \
	$(REPO_DIR)/enrichments/tsdoc_v1/realtime.json \
	$(REPO_DIR)/enrichments/tsdoc_v1/storage.json \
	$(REPO_DIR)/enrichments/tsdoc_v1/functions.json \
	> $(REPO_DIR)/enrichments/tsdoc_v1/combined.json

combine.tsdoc.v2:
	jq -s '{ name: "Combined Specs", children: [.[0], .[1], .[2], .[3], .[4], .[5]] }' \
	$(REPO_DIR)/enrichments/tsdoc_v2/supabase_dereferenced.json \
	$(REPO_DIR)/enrichments/tsdoc_v2/gotrue_dereferenced.json \
	$(REPO_DIR)/enrichments/tsdoc_v2/postgrest_dereferenced.json \
	$(REPO_DIR)/enrichments/tsdoc_v2/realtime_dereferenced.json \
	$(REPO_DIR)/enrichments/tsdoc_v2/storage_dereferenced.json \
	$(REPO_DIR)/enrichments/tsdoc_v2/functions_dereferenced.json \
	> $(REPO_DIR)/enrichments/tsdoc_v2/combined.json

###############################################################################
# Format everything - easier for git to track changes
###############################################################################
format:
	npx prettier --write .

