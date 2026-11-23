# gNB certificate templates

Copies of `gnb/` flows that match a row in the gNB black-box certificate path table.
The original stays in `gnb/`. Runik loads flows by the YAML `name:` field, so these copies are not a second flow.

A file name is `Pxxx-ts38413-<clause>-<procedure>-<path>--<flow>.yaml`.
The path-table row is the part before `--`. The flow name is the part after.

195 copies, covering 38 of 165 path rows.
A row with a copy is marked `template` in the Runik column of the path table. That mark means a template exists. It does not mean a core has passed it.

Seven `gnb/` flows have no certificate procedure and were not copied: `amf_ha_resume`, `fuzz_ngap_initial_ue_structural`, `gnb_hold`, `malformed_nas`, `nas_count_start_zero_never_reset`, `no_null_integrity_downgrade_on_existing_context`, `security_key_matches_derived_kgnb`.

| Path | Procedure | Outcome | Flow | Source | How matched |
|---|---|---|---|---|---|
| P045 | Downlink NAS Transport | success | `auth_failure_mac` | `gnb/auth_failure_mac.yaml` | filename |
| P045 | Downlink NAS Transport | success | `auth_failure_sync` | `gnb/auth_failure_sync.yaml` | filename |
| P045 | Downlink NAS Transport | success | `auth_request_5g_aka_ies` | `gnb/auth_request_5g_aka_ies.yaml` | filename |
| P045 | Downlink NAS Transport | success | `auth_request_abba_value` | `gnb/auth_request_abba_value.yaml` | filename |
| P045 | Downlink NAS Transport | success | `auth_request_ngksi_differs` | `gnb/auth_request_ngksi_differs.yaml` | filename |
| P045 | Downlink NAS Transport | success | `auth_res_star_accepted_smc` | `gnb/auth_res_star_accepted_smc.yaml` | filename |
| P045 | Downlink NAS Transport | success | `auth_response_wrong_res_star` | `gnb/auth_response_wrong_res_star.yaml` | filename |
| P045 | Downlink NAS Transport | success | `configuration_update` | `gnb/configuration_update.yaml` | filename |
| P045 | Downlink NAS Transport | success | `configuration_update_optional` | `gnb/configuration_update_optional.yaml` | filename |
| P044 | Initial UE Message | success | `deregistration` | `gnb/deregistration.yaml` | filename |
| P044 | Initial UE Message | success | `deregistration_after_release` | `gnb/deregistration_after_release.yaml` | filename |
| P044 | Initial UE Message | success | `deregistration_switchoff` | `gnb/deregistration_switchoff.yaml` | filename |
| P044 | Initial UE Message | success | `deregistration_tears_down_user_plane` | `gnb/deregistration_tears_down_user_plane.yaml` | filename |
| P044 | Initial UE Message | success | `deregistration_unprotected_guti_ignored` | `gnb/deregistration_unprotected_guti_ignored.yaml` | filename |
| P044 | Initial UE Message | success | `duplicate_registration` | `gnb/duplicate_registration.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `e2e_cycle` | `gnb/e2e_cycle.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `e2e_cycle_signalling` | `gnb/e2e_cycle_signalling.yaml` | filename |
| P044 | Initial UE Message | success | `emergency_pdu_normal_ue` | `gnb/emergency_pdu_normal_ue.yaml` | filename |
| P044 | Initial UE Message | success | `emergency_registration` | `gnb/emergency_registration.yaml` | filename |
| P044 | Initial UE Message | success | `emergency_registration_and_pdu` | `gnb/emergency_registration_and_pdu.yaml` | filename |
| P044 | Initial UE Message | success | `emergency_unauth_imei` | `gnb/emergency_unauth_imei.yaml` | filename |
| P044 | Initial UE Message | success | `emergency_unauth_refused` | `gnb/emergency_unauth_refused.yaml` | filename |
| P059 | Error Indication | success | `error_indication` | `gnb/error_indication.yaml` | clause 8.7.5 |
| P059 | Error Indication | success | `error_indication_non_ue_associated_after_setup` | `gnb/error_indication_non_ue_associated_after_setup.yaml` | clause 8.7.5 |
| P059 | Error Indication | success | `error_indication_unknown_ue_ids` | `gnb/error_indication_unknown_ue_ids.yaml` | clause 8.7.5 |
| P033 | Handover Cancellation | success | `handover_cancel` | `gnb/handover_cancel.yaml` | clause 8.4.5 |
| P033 | Handover Cancellation | success | `handover_cancel_source_user_plane_survives` | `gnb/handover_cancel_source_user_plane_survives.yaml` | clause 8.4.5 |
| P029 | Handover Preparation | success | `handover_source` | `gnb/handover_source.yaml` | filename |
| P045 | Downlink NAS Transport | success | `identity_request` | `gnb/identity_request.yaml` | filename |
| P045 | Downlink NAS Transport | success | `identity_request_type_suci` | `gnb/identity_request_type_suci.yaml` | filename |
| P045 | Downlink NAS Transport | success | `identity_response_wrong_type` | `gnb/identity_response_wrong_type.yaml` | filename |
| P011 | Initial Context Setup | failure | `initial_context_setup_failure_no_success_signalling` | `gnb/initial_context_setup_failure_no_success_signalling.yaml` | clause 8.3.1 |
| P011 | Initial Context Setup | failure | `initial_context_setup_failure_unsupported_security` | `gnb/initial_context_setup_failure_unsupported_security.yaml` | clause 8.3.1 |
| P010 | Initial Context Setup | success | `initial_context_setup_security_key_and_capabilities` | `gnb/initial_context_setup_security_key_and_capabilities.yaml` | clause 8.3.1 |
| P010 | Initial Context Setup | success | `initial_context_setup_ue_security_capabilities_unmodified` | `gnb/initial_context_setup_ue_security_capabilities_unmodified.yaml` | filename |
| P044 | Initial UE Message | success | `initial_ue_message_ue_context_request` | `gnb/initial_ue_message_ue_context_request.yaml` | clause 8.6.1 |
| P045 | Downlink NAS Transport | success | `malformed_auth_response_mid_procedure` | `gnb/malformed_auth_response_mid_procedure.yaml` | filename |
| P044 | Initial UE Message | success | `mobility_registration_updating` | `gnb/mobility_registration_updating.yaml` | filename |
| P044 | Initial UE Message | success | `mobility_update_guti_reallocation` | `gnb/mobility_update_guti_reallocation.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `mobility_update_pdu_session_status_sync` | `gnb/mobility_update_pdu_session_status_sync.yaml` | filename |
| P044 | Initial UE Message | success | `mobility_update_protocol_error_rejected` | `gnb/mobility_update_protocol_error_rejected.yaml` | filename |
| P044 | Initial UE Message | success | `mobility_update_uplink_data_status_reactivates` | `gnb/mobility_update_uplink_data_status_reactivates.yaml` | filename |
| P033 | Handover Cancellation | success | `n2_handover_cancel_no_preparation` | `gnb/n2_handover_cancel_no_preparation.yaml` | clause 8.4.5 |
| P029 | Handover Preparation | success | `n2_handover_command_pdu_session_handover_list` | `gnb/n2_handover_command_pdu_session_handover_list.yaml` | clause 8.4.1 |
| P029 | Handover Preparation | success | `n2_handover_command_relays_target_container` | `gnb/n2_handover_command_relays_target_container.yaml` | clause 8.4.1 |
| P032 | Handover Resource Allocation | failure | `n2_handover_failure_no_admitted_resources` | `gnb/n2_handover_failure_no_admitted_resources.yaml` | clause 8.4.2 |
| P032 | Handover Resource Allocation | failure | `n2_handover_failure_unsupported_encryption` | `gnb/n2_handover_failure_unsupported_encryption.yaml` | clause 8.4.2 |
| P032 | Handover Resource Allocation | failure | `n2_handover_failure_unsupported_integrity` | `gnb/n2_handover_failure_unsupported_integrity.yaml` | clause 8.4.2 |
| P031 | Handover Resource Allocation | success | `n2_handover_mobility_registration_update` | `gnb/n2_handover_mobility_registration_update.yaml` | clause 8.4.2 |
| P036 | Handover Notification | success | `n2_handover_nas_served_on_target_association` | `gnb/n2_handover_nas_served_on_target_association.yaml` | clause 8.4.3 |
| P036 | Handover Notification | success | `n2_handover_notify_releases_source_context` | `gnb/n2_handover_notify_releases_source_context.yaml` | clause 8.4.3 |
| P030 | Handover Preparation | failure | `n2_handover_preparation_failure_unknown_target` | `gnb/n2_handover_preparation_failure_unknown_target.yaml` | clause 8.4.1 |
| P031 | Handover Resource Allocation | success | `n2_handover_request_pdu_session_list` | `gnb/n2_handover_request_pdu_session_list.yaml` | clause 8.4.2 |
| P031 | Handover Resource Allocation | success | `n2_handover_request_security_context_and_caps` | `gnb/n2_handover_request_security_context_and_caps.yaml` | clause 8.4.2 |
| P029 | Handover Preparation | success | `n2_handover_required_no_pdu_session` | `gnb/n2_handover_required_no_pdu_session.yaml` | clause 8.4.1 |
| P030 | Handover Preparation | failure | `n2_handover_required_stale_amf_ue_ngap_id` | `gnb/n2_handover_required_stale_amf_ue_ngap_id.yaml` | filename |
| P048 | Reroute NAS Request | success | `nas_non_delivery` | `gnb/nas_non_delivery.yaml` | clause 8.6.5 |
| P056 | NG Reset | success | `ng_reset` | `gnb/ng_reset.yaml` | clause 8.7.4 |
| P056 | NG Reset | success | `ng_reset_partial` | `gnb/ng_reset_partial.yaml` | clause 8.7.4 |
| P056 | NG Reset | success | `ng_reset_preserves_application_config` | `gnb/ng_reset_preserves_application_config.yaml` | clause 8.7.4 |
| P056 | NG Reset | success | `ng_reset_then_recover` | `gnb/ng_reset_then_recover.yaml` | clause 8.7.4 |
| P049 | NG Setup | success | `ng_setup` | `gnb/ng_setup.yaml` | clause 8.7.1 |
| P050 | NG Setup | failure | `ng_setup_failure` | `gnb/ng_setup_failure.yaml` | clause 8.7.1 |
| P050 | NG Setup | failure | `ng_setup_missing_supported_ta` | `gnb/ng_setup_missing_supported_ta.yaml` | filename |
| P049 | NG Setup | success | `ng_setup_multi_tac_multi_slice` | `gnb/ng_setup_multi_tac_multi_slice.yaml` | clause 8.7.1 |
| P049 | NG Setup | success | `ng_setup_response_mandatory_ies` | `gnb/ng_setup_response_mandatory_ies.yaml` | filename |
| P050 | NG Setup | failure | `ng_setup_unsupported_slice` | `gnb/ng_setup_unsupported_slice.yaml` | clause 8.7.1 |
| P044 | Initial UE Message | success | `paging_no_response_retransmission` | `gnb/paging_no_response_retransmission.yaml` | clause 8.6.1 |
| P042 | Paging | success | `paging_response` | `gnb/paging_response.yaml` | clause 8.5.1 |
| P034 | Path Switch Request | success | `path_switch` | `gnb/path_switch.yaml` | clause 8.4.4 |
| P009 | PDU Session Resource Notify | success | `pdu_session_af_qos_modify` | `gnb/pdu_session_af_qos_modify.yaml` | clause 8.2.4 |
| P009 | PDU Session Resource Notify | success | `pdu_session_amf_initiated_modify` | `gnb/pdu_session_amf_initiated_modify.yaml` | clause 8.2.4 |
| P001 | PDU Session Resource Setup | success | `pdu_session_default_qos_rule_and_ambr` | `gnb/pdu_session_default_qos_rule_and_ambr.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_duplicate_id_establishment` | `gnb/pdu_session_duplicate_id_establishment.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_duplicate_initial_request` | `gnb/pdu_session_duplicate_initial_request.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_epco_dns_request` | `gnb/pdu_session_epco_dns_request.yaml` | filename |
| P012 | UE Context Release | success | `pdu_session_gone_after_ue_context_release` | `gnb/pdu_session_gone_after_ue_context_release.yaml` | filename |
| P003 | PDU Session Resource Release | success | `pdu_session_id_reuse_after_release` | `gnb/pdu_session_id_reuse_after_release.yaml` | clause 8.2.2 |
| P001 | PDU Session Resource Setup | success | `pdu_session_ipv4v6_dual_stack_traffic` | `gnb/pdu_session_ipv4v6_dual_stack_traffic.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_ipv6_granted` | `gnb/pdu_session_ipv6_granted.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_ipv6_ra_ping6` | `gnb/pdu_session_ipv6_ra_ping6.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_ipv6_udp6` | `gnb/pdu_session_ipv6_udp6.yaml` | filename |
| P002 | PDU Session Resource Setup | failure | `pdu_session_modification_unknown_psi` | `gnb/pdu_session_modification_unknown_psi.yaml` | filename |
| P009 | PDU Session Resource Notify | success | `pdu_session_modify_partial_reject` | `gnb/pdu_session_modify_partial_reject.yaml` | clause 8.2.4 |
| P002 | PDU Session Resource Setup | failure | `pdu_session_reject` | `gnb/pdu_session_reject.yaml` | filename |
| P002 | PDU Session Resource Setup | failure | `pdu_session_reject_slice` | `gnb/pdu_session_reject_slice.yaml` | filename |
| P002 | PDU Session Resource Setup | failure | `pdu_session_reject_unsupported_type` | `gnb/pdu_session_reject_unsupported_type.yaml` | filename |
| P003 | PDU Session Resource Release | success | `pdu_session_release` | `gnb/pdu_session_release.yaml` | clause 8.2.2 |
| P004 | PDU Session Resource Release | failure | `pdu_session_release_for_unknown_session` | `gnb/pdu_session_release_for_unknown_session.yaml` | filename |
| P003 | PDU Session Resource Release | success | `pdu_session_release_one_of_two` | `gnb/pdu_session_release_one_of_two.yaml` | clause 8.2.2 |
| P006 | PDU Session Resource Modify | failure | `pdu_session_release_response_wrong_session_id` | `gnb/pdu_session_release_response_wrong_session_id.yaml` | clause 8.2.3 |
| P007 | PDU Session Resource Modify Indication | success | `pdu_session_resource_notify_qos_flow_release` | `gnb/pdu_session_resource_notify_qos_flow_release.yaml` | clause 8.2.5 |
| P003 | PDU Session Resource Release | success | `pdu_session_resource_release_command_n2` | `gnb/pdu_session_resource_release_command_n2.yaml` | clause 8.2.2 |
| P001 | PDU Session Resource Setup | success | `pdu_session_setup` | `gnb/pdu_session_setup.yaml` | clause 8.2.1 |
| P001 | PDU Session Resource Setup | success | `pdu_session_setup_ipv4v6` | `gnb/pdu_session_setup_ipv4v6.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_setup_ipv6` | `gnb/pdu_session_setup_ipv6.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_slice_snssai_echoed` | `gnb/pdu_session_slice_snssai_echoed.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_ssc_mode1_selected` | `gnb/pdu_session_ssc_mode1_selected.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_ssc_mode_default_selected` | `gnb/pdu_session_ssc_mode_default_selected.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_two_concurrent_multi_dnn` | `gnb/pdu_session_two_concurrent_multi_dnn.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_two_concurrent_same_dnn` | `gnb/pdu_session_two_concurrent_same_dnn.yaml` | filename |
| P005 | PDU Session Resource Modify | success | `pdu_session_ue_requested_modify` | `gnb/pdu_session_ue_requested_modify.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `pdu_session_upf_teid_non_zero` | `gnb/pdu_session_upf_teid_non_zero.yaml` | filename |
| P044 | Initial UE Message | success | `periodic_registration_updating` | `gnb/periodic_registration_updating.yaml` | filename |
| P044 | Initial UE Message | success | `periodic_update_without_amf_context` | `gnb/periodic_update_without_amf_context.yaml` | filename |
| P045 | Downlink NAS Transport | success | `post_auth_reject_unprotected_uplink` | `gnb/post_auth_reject_unprotected_uplink.yaml` | filename |
| P046 | Uplink NAS Transport | success | `post_smc_backwards_count_uplink_nas` | `gnb/post_smc_backwards_count_uplink_nas.yaml` | filename |
| P045 | Downlink NAS Transport | success | `post_smc_bad_mac_uplink_discarded` | `gnb/post_smc_bad_mac_uplink_discarded.yaml` | filename |
| P045 | Downlink NAS Transport | success | `post_smc_unprotected_uplink_discarded` | `gnb/post_smc_unprotected_uplink_discarded.yaml` | filename |
| P053 | AMF Configuration Update | success | `ran_config_update` | `gnb/ran_config_update.yaml` | clause 8.7.3 |
| P051 | RAN Configuration Update | success | `ran_config_update_omitted_ie_preserves_config` | `gnb/ran_config_update_omitted_ie_preserves_config.yaml` | clause 8.7.2 |
| P052 | RAN Configuration Update | failure | `ran_config_update_unserved_plmn_refused` | `gnb/ran_config_update_unserved_plmn_refused.yaml` | clause 8.7.2 |
| P044 | Initial UE Message | success | `registration` | `gnb/registration.yaml` | filename |
| P044 | Initial UE Message | success | `registration_accept_ciphered_after_smc` | `gnb/registration_accept_ciphered_after_smc.yaml` | filename |
| P044 | Initial UE Message | success | `registration_accept_t3512` | `gnb/registration_accept_t3512.yaml` | filename |
| P044 | Initial UE Message | success | `registration_accept_tai_list` | `gnb/registration_accept_tai_list.yaml` | filename |
| P044 | Initial UE Message | success | `registration_after_deregistration` | `gnb/registration_after_deregistration.yaml` | filename |
| P044 | Initial UE Message | success | `registration_ciphering_non_null` | `gnb/registration_ciphering_non_null.yaml` | filename |
| P044 | Initial UE Message | success | `registration_empty_mandatory_identity` | `gnb/registration_empty_mandatory_identity.yaml` | filename |
| P044 | Initial UE Message | success | `registration_follow_on_keeps_connection` | `gnb/registration_follow_on_keeps_connection.yaml` | filename |
| P044 | Initial UE Message | success | `registration_from_unserved_tracking_area_rejected` | `gnb/registration_from_unserved_tracking_area_rejected.yaml` | filename |
| P044 | Initial UE Message | success | `registration_guti_reallocation` | `gnb/registration_guti_reallocation.yaml` | filename |
| P044 | Initial UE Message | success | `registration_guti_unknown` | `gnb/registration_guti_unknown.yaml` | filename |
| P044 | Initial UE Message | success | `registration_initial_with_native_guti` | `gnb/registration_initial_with_native_guti.yaml` | filename |
| P044 | Initial UE Message | success | `registration_malformed` | `gnb/registration_malformed.yaml` | filename |
| P044 | Initial UE Message | success | `registration_nssf_allowed` | `gnb/registration_nssf_allowed.yaml` | filename |
| P044 | Initial UE Message | success | `registration_nssf_fallback` | `gnb/registration_nssf_fallback.yaml` | filename |
| P044 | Initial UE Message | success | `registration_null_scheme_suci` | `gnb/registration_null_scheme_suci.yaml` | filename |
| P044 | Initial UE Message | success | `registration_null_scheme_when_concealment_required` | `gnb/registration_null_scheme_when_concealment_required.yaml` | filename |
| P044 | Initial UE Message | success | `registration_on_existing_native_context` | `gnb/registration_on_existing_native_context.yaml` | filename |
| P044 | Initial UE Message | success | `registration_replay_old_security_context` | `gnb/registration_replay_old_security_context.yaml` | filename |
| P044 | Initial UE Message | success | `registration_simple` | `gnb/registration_simple.yaml` | filename |
| P044 | Initial UE Message | success | `registration_stale_context_forces_reauthentication` | `gnb/registration_stale_context_forces_reauthentication.yaml` | filename |
| P044 | Initial UE Message | success | `registration_successive_distinct_guti` | `gnb/registration_successive_distinct_guti.yaml` | filename |
| P044 | Initial UE Message | success | `registration_suci_profile_a` | `gnb/registration_suci_profile_a.yaml` | filename |
| P044 | Initial UE Message | success | `registration_suci_profile_b` | `gnb/registration_suci_profile_b.yaml` | filename |
| P044 | Initial UE Message | success | `registration_unknown_ie` | `gnb/registration_unknown_ie.yaml` | filename |
| P044 | Initial UE Message | success | `registration_unprovisioned_subscriber_rejected` | `gnb/registration_unprovisioned_subscriber_rejected.yaml` | filename |
| P044 | Initial UE Message | success | `registration_zero_security_capabilities` | `gnb/registration_zero_security_capabilities.yaml` | filename |
| P045 | Downlink NAS Transport | success | `security_mode_bidding_down` | `gnb/security_mode_bidding_down.yaml` | filename |
| P045 | Downlink NAS Transport | success | `security_mode_command_new_context` | `gnb/security_mode_command_new_context.yaml` | filename |
| P045 | Downlink NAS Transport | success | `security_mode_command_replayed_ue_capabilities` | `gnb/security_mode_command_replayed_ue_capabilities.yaml` | filename |
| P045 | Downlink NAS Transport | success | `security_mode_reject_aborts_registration` | `gnb/security_mode_reject_aborts_registration.yaml` | filename |
| P045 | Downlink NAS Transport | success | `security_mode_selected_algorithms_advertised` | `gnb/security_mode_selected_algorithms_advertised.yaml` | filename |
| P044 | Initial UE Message | success | `service_request` | `gnb/service_request.yaml` | filename |
| P044 | Initial UE Message | success | `service_request_bad_mac_service_reject_cause9` | `gnb/service_request_bad_mac_service_reject_cause9.yaml` | filename |
| P044 | Initial UE Message | success | `service_request_emergency` | `gnb/service_request_emergency.yaml` | filename |
| P044 | Initial UE Message | success | `service_request_high_priority` | `gnb/service_request_high_priority.yaml` | filename |
| P044 | Initial UE Message | success | `service_request_malformed` | `gnb/service_request_malformed.yaml` | filename |
| P044 | Initial UE Message | success | `service_request_nas_container_uplink_data_status` | `gnb/service_request_nas_container_uplink_data_status.yaml` | filename |
| P044 | Initial UE Message | success | `service_request_no_5gmm_context_rejected` | `gnb/service_request_no_5gmm_context_rejected.yaml` | filename |
| P044 | Initial UE Message | success | `service_request_pdu_session_status_sync` | `gnb/service_request_pdu_session_status_sync.yaml` | filename |
| P044 | Initial UE Message | success | `service_request_reactivates_user_plane` | `gnb/service_request_reactivates_user_plane.yaml` | filename |
| P044 | Initial UE Message | success | `service_request_second_gnb_releases_old_connection` | `gnb/service_request_second_gnb_releases_old_connection.yaml` | clause 8.6.1 |
| P044 | Initial UE Message | success | `service_request_signalling` | `gnb/service_request_signalling.yaml` | filename |
| P044 | Initial UE Message | success | `service_request_unknown_tmsi_rejected` | `gnb/service_request_unknown_tmsi_rejected.yaml` | filename |
| P044 | Initial UE Message | success | `service_request_with_uplink_data_status` | `gnb/service_request_with_uplink_data_status.yaml` | filename |
| P045 | Downlink NAS Transport | success | `smc_complete_unciphered_when_cipher_selected` | `gnb/smc_complete_unciphered_when_cipher_selected.yaml` | filename |
| P045 | Downlink NAS Transport | success | `smc_null_integrity_rejected_nonemergency` | `gnb/smc_null_integrity_rejected_nonemergency.yaml` | filename |
| P045 | Downlink NAS Transport | success | `smc_replay_stale_count` | `gnb/smc_replay_stale_count.yaml` | filename |
| P045 | Downlink NAS Transport | success | `smc_wrong_mac_rejected` | `gnb/smc_wrong_mac_rejected.yaml` | filename |
| P044 | Initial UE Message | success | `sms_deregistered_by_mobility_update` | `gnb/sms_deregistered_by_mobility_update.yaml` | filename |
| P046 | Uplink NAS Transport | success | `sms_mo` | `gnb/sms_mo.yaml` | filename |
| P046 | Uplink NAS Transport | success | `sms_mo_loopback` | `gnb/sms_mo_loopback.yaml` | filename |
| P044 | Initial UE Message | success | `sms_requested_registration_sms_allowed` | `gnb/sms_requested_registration_sms_allowed.yaml` | filename |
| P046 | Uplink NAS Transport | success | `sms_uplink_without_smsf_association_aborted` | `gnb/sms_uplink_without_smsf_association_aborted.yaml` | filename |
| P014 | UE Context Modification | failure | `ue_context_modification_failure_rx_reject` | `gnb/ue_context_modification_failure_rx_reject.yaml` | clause 8.3.4 |
| P013 | UE Context Modification | success | `ue_context_modification_request_rx_handle_and_respond` | `gnb/ue_context_modification_request_rx_handle_and_respond.yaml` | clause 8.3.4 |
| P012 | UE Context Release | success | `ue_context_release_amf_initiated` | `gnb/ue_context_release_amf_initiated.yaml` | clause 8.3.3 |
| P012 | UE Context Release | success | `ue_context_release_complete_without_command_unsolicited` | `gnb/ue_context_release_complete_without_command_unsolicited.yaml` | clause 8.3.3 |
| P021 | UE Context Release Request | success | `ue_context_release_request_gnb_initiated` | `gnb/ue_context_release_request_gnb_initiated.yaml` | clause 8.3.2 |
| P021 | UE Context Release Request | success | `ue_context_release_request_unknown_amf_ue_ngap_id` | `gnb/ue_context_release_request_unknown_amf_ue_ngap_id.yaml` | clause 8.3.2 |
| P017 | UE Context Resume | success | `ue_context_resume` | `gnb/ue_context_resume.yaml` | filename |
| P023 | Connection Establishment Indication | success | `ue_context_resume_request_without_prior_suspend` | `gnb/ue_context_resume_request_without_prior_suspend.yaml` | clause 8.3.6 |
| P022 | RRC Inactive Transition Report | success | `ue_context_suspend` | `gnb/ue_context_suspend.yaml` | clause 8.3.5 |
| P045 | Downlink NAS Transport | success | `ue_policy_delivery` | `gnb/ue_policy_delivery.yaml` | filename |
| P044 | Initial UE Message | success | `ue_policy_state_indication_in_registration` | `gnb/ue_policy_state_indication_in_registration.yaml` | filename |
| P045 | Downlink NAS Transport | success | `ue_policy_truncated_updp_ignored` | `gnb/ue_policy_truncated_updp_ignored.yaml` | filename |
| P045 | Downlink NAS Transport | success | `ue_policy_uplink_no_pcf_association_aborted` | `gnb/ue_policy_uplink_no_pcf_association_aborted.yaml` | filename |
| P001 | PDU Session Resource Setup | success | `uplane_downlink_pdu_session_container` | `gnb/uplane_downlink_pdu_session_container.yaml` | filename |
| P133 | Transfer of UL PDU Session Information | success | `uplane_mtu_sized_df_probe` | `gnb/uplane_mtu_sized_df_probe.yaml` | filename |
| P133 | Transfer of UL PDU Session Information | success | `uplane_neighbor_teid` | `gnb/uplane_neighbor_teid.yaml` | filename |
| P133 | Transfer of UL PDU Session Information | success | `uplane_traffic` | `gnb/uplane_traffic.yaml` | filename |
| P133 | Transfer of UL PDU Session Information | success | `uplane_unauthorised_qfi_discarded` | `gnb/uplane_unauthorised_qfi_discarded.yaml` | filename |
| P046 | Uplink NAS Transport | success | `uplink_nas_after_context_release_stale_ids` | `gnb/uplink_nas_after_context_release_stale_ids.yaml` | filename |
| P046 | Uplink NAS Transport | success | `uplink_nas_before_security_activation` | `gnb/uplink_nas_before_security_activation.yaml` | filename |
| P046 | Uplink NAS Transport | success | `uplink_nas_transport_established_connection` | `gnb/uplink_nas_transport_established_connection.yaml` | clause 8.6.3 |
| P034 | Path Switch Request | success | `xn_path_switch_ack_nh_ncc_increment` | `gnb/xn_path_switch_ack_nh_ncc_increment.yaml` | clause 8.4.4 |
| P034 | Path Switch Request | success | `xn_path_switch_ack_ul_tnl_information` | `gnb/xn_path_switch_ack_ul_tnl_information.yaml` | clause 8.4.4 |
| P034 | Path Switch Request | success | `xn_path_switch_duplicate_pdu_session_id` | `gnb/xn_path_switch_duplicate_pdu_session_id.yaml` | clause 8.4.4 |
| P035 | Path Switch Request | failure | `xn_path_switch_failure_no_switchable_session` | `gnb/xn_path_switch_failure_no_switchable_session.yaml` | clause 8.4.4 |
| P035 | Path Switch Request | failure | `xn_path_switch_failure_unknown_source_amf_id` | `gnb/xn_path_switch_failure_unknown_source_amf_id.yaml` | clause 8.4.4 |
| P035 | Path Switch Request | failure | `xn_path_switch_missing_ue_security_capabilities` | `gnb/xn_path_switch_missing_ue_security_capabilities.yaml` | filename |
| P034 | Path Switch Request | success | `xn_path_switch_two_switches_ncc_increases` | `gnb/xn_path_switch_two_switches_ncc_increases.yaml` | clause 8.4.4 |
| P035 | Path Switch Request | failure | `xn_path_switch_unknown_pdu_session` | `gnb/xn_path_switch_unknown_pdu_session.yaml` | clause 8.4.4 |
