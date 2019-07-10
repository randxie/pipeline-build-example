load("@bazel_tools//tools/build_defs/pkg:pkg.bzl", "pkg_deb", "pkg_tar")
load("//:global_vars.bzl", "GCS_BUCKET", "PROJECT_ID", "REPO_NAME")
load("//common/build_rules:pipeline_creator.bzl", "pipeline_creator")
load("//common/build_rules:ref_replacer.bzl", "ref_replacer")


COMPONENT_NAME = REPO_NAME + "/" + package_name()

GCS_DATA_DIR = GCS_BUCKET + "/" + COMPONENT_NAME + "/data"

TARGET_FOLDER = select({
    "//common/build_rules:dry_run_mode": "/tmp" + "/" + COMPONENT_NAME,
    "//common/build_rules:prod_mode": GCS_BUCKET + "/" + COMPONENT_NAME,
    "//conditions:default": "/tmp" + "/" + COMPONENT_NAME,
})


# release component yaml
ref_replacer(
    name = "release_component_yaml",
    srcs = ["component.yaml"],
    outs = ["RELEASE_component.yaml"],
    container_refs = [
        "gcr.io/{}/{}:latest".format(PROJECT_ID, COMPONENT_NAME),
    ],
    str_replace_json = {
        "__CONTAINER_URI__": "gcr.io/{}/{}:latest".format(PROJECT_ID, COMPONENT_NAME),
    },
)