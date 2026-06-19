WEBRPC_GEN_VERSION := v0.37.2
GEN := go run github.com/webrpc/webrpc/cmd/webrpc-gen@$(WEBRPC_GEN_VERSION)

.PHONY: all generate diff test test-streaming test-keywords

all: generate diff test test-streaming test-keywords

generate:
	$(GEN) -schema=example.ridl -target=.. -client -server -out=./example.gen.py

diff:
	git diff --exit-code -- example.gen.py

test:
	python3 test_example.py

test-streaming:
	@echo "verifying streaming schemas are rejected"
	@if $(GEN) -schema=streaming.ridl -target=.. -client -server -out=/dev/null >/dev/null 2>&1; then \
		echo "FAIL: streaming schema was not rejected"; exit 1; \
	else \
		echo "OK: streaming rejected"; \
	fi

test-keywords:
	@echo "verifying Python-keyword identifiers generate valid Python"
	@out=$$(mktemp); \
	trap 'rm -f "$$out"' EXIT; \
	set -e; \
	$(GEN) -schema=keywords.ridl -target=.. -client -server -out=$$out >/dev/null; \
	python3 -m py_compile "$$out"; \
	echo "OK: keyword identifiers compile"
